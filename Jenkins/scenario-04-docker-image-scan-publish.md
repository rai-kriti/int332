# Scenario 4: Docker Image Build, Scan, and Publish with Dynamic Tags

> **Difficulty:** Intermediate | **Topic:** Docker, GHCR, Trivy CVE scanning, dynamic image tags | **Time to read:** ~20 min

---

## The Story

Your security team ran an audit and found two serious problems:

1. All Docker images in production are tagged `latest` — nobody knows which code version is running
2. A container with a known **critical vulnerability** (CVE) was running in production for 3 months undetected

Your task: Build a pipeline that tags images with traceable identifiers, **scans every image for vulnerabilities before pushing**, and only pushes to the registry from the `main` branch.

---

## What You Will Learn

- How to create **meaningful, traceable Docker image tags** using Git commit SHA + build number
- How to run **Trivy** (a popular open-source container vulnerability scanner) inside the pipeline
- How to **authenticate and push** to GitHub Container Registry (GHCR)
- Why `|| true` is needed in cleanup steps
- How credentials work for Docker registry login (without exposing passwords in logs)

---

## Core Concept 1: Why `latest` Tags Are Dangerous

When every image is tagged `latest`, you lose all traceability:

```
Image running in production: myapp:latest
What commit is that? Nobody knows.
When was it built? Nobody knows.
Which build? Nobody knows.
```

With a meaningful tag: `myapp:abc12345-42`
- `abc12345` = first 8 characters of the Git commit SHA
- `42` = Jenkins build number

Now if something breaks in production, you immediately know:
- `abc12345` = look at commit `abc12345` in Git → see exactly what changed
- `42` = look at Jenkins build #42 → see full build log, test results, who triggered it

---

## Core Concept 2: Extracting the Short Commit SHA

Jenkins provides `GIT_COMMIT` which is the full 40-character SHA like `abc123456789def0...`. For image tags we only want the first 8 characters.

In Groovy (the language Jenkinsfiles use):

```groovy
// Option 1: String slicing (0..7 means characters at index 0 through 7)
def shortSha = GIT_COMMIT[0..7]
// "abc123456789def0..." → "abc12345"

// Option 2: take() method
def shortSha = GIT_COMMIT.take(8)
// Same result

// Option 3: In environment block (uses Groovy expression inside "")
environment {
    IMAGE_TAG = "${GIT_COMMIT[0..7]}-${BUILD_NUMBER}"
    // Result: "abc12345-42"
}
```

---

## Core Concept 3: What is Trivy?

Trivy is a **container image vulnerability scanner** made by Aqua Security. It scans:
- The OS packages inside your image (e.g., outdated libssl on Ubuntu)
- Application dependencies (e.g., a vulnerable version of log4j)
- Secret files accidentally baked into images

Trivy works by:
1. Pulling your image
2. Listing every installed package inside it
3. Checking each package against a vulnerability database (like CVE databases)
4. Reporting findings by severity: `UNKNOWN`, `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`

**In Jenkins**, we run Trivy as a Docker container (no installation needed):

```bash
docker run --rm \
    -v /var/run/docker.sock:/var/run/docker.sock \
    aquasec/trivy:latest image \
    --exit-code 1 \            # Return exit code 1 (failure) if vulnerabilities found
    --severity HIGH,CRITICAL \ # Only care about HIGH and CRITICAL
    --no-progress \            # Don't show progress bars (cleaner logs)
    myapp:abc12345-42
```

The `-v /var/run/docker.sock:/var/run/docker.sock` part gives the Trivy container access to the host's Docker daemon so it can inspect the image we just built.

**`--exit-code 1`**: Without this, Trivy finds vulnerabilities but exits with code 0 (success). Jenkins only fails a step if the exit code is non-zero. The `--exit-code 1` flag tells Trivy: "if you find any HIGH or CRITICAL CVEs, exit with code 1 so Jenkins fails the build."

---

## Core Concept 4: GitHub Container Registry (GHCR)

GHCR is GitHub's built-in Docker registry (like Docker Hub, but hosted by GitHub). Images are stored at `ghcr.io/your-org/image-name:tag`.

**Authentication:** You need a GitHub token with `write:packages` permission.

In Jenkins:
1. Create a GitHub Personal Access Token (PAT) with `write:packages` scope
2. Store it in Jenkins as a "Secret text" credential with ID `github-container-token`
3. In the pipeline, inject it and use it to log in

**Secure login pattern:**

```bash
# WRONG — password visible in process list
docker login ghcr.io -u myorg -p mytoken

# RIGHT — password piped via stdin (never in process args)
echo $GH_TOKEN | docker login ghcr.io -u myorg --password-stdin
```

---

## Core Concept 5: `|| true` in Cleanup

When cleaning up Docker images in `post { always {} }`, you might try to remove an image that doesn't exist (e.g., if the build failed before the image was created). In that case, `docker rmi` would fail and Jenkins would mark the `post{}` block as failed — even though the actual issue was handled earlier.

```bash
# If FULL_IMAGE doesn't exist, this command exits with code 1 (error)
docker rmi myapp:abc12345-42

# The || true means: "if the command fails, that's OK — treat it as success"
# This is the bash OR operator — if left side fails, right side runs (true always succeeds)
docker rmi myapp:abc12345-42 || true
```

---

## Step-by-Step Solution Build

### Step 1 — Set up environment variables

```groovy
pipeline {
    agent any

    environment {
        // Registry details
        REGISTRY   = 'ghcr.io'
        ORG        = 'my-org'          // Your GitHub organization or username
        APP        = 'myapp'           // Your application name

        // Dynamic tag: short commit SHA + Jenkins build number
        // Example: "abc12345-42"
        // GIT_COMMIT[0..7] = first 8 characters of the commit SHA
        IMAGE_TAG  = "${GIT_COMMIT[0..7]}-${BUILD_NUMBER}"

        // Full image path: ghcr.io/my-org/myapp:abc12345-42
        FULL_IMAGE = "${REGISTRY}/${ORG}/${APP}:${IMAGE_TAG}"

        // GitHub token from Jenkins credentials store (masked in logs)
        GH_TOKEN   = credentials('github-container-token')
    }
```

### Step 2 — Checkout stage

```groovy
        stage('Checkout') {
            steps {
                checkout scm
                // After this, we have the Dockerfile and application code
                // GIT_COMMIT is now set to the current commit's SHA
                echo "Building commit: ${GIT_COMMIT}"
                echo "Image will be tagged: ${FULL_IMAGE}"
            }
        }
```

### Step 3 — Build the Docker image

```groovy
        stage('Build Docker Image') {
            steps {
                // docker build -t TAG .
                // -t = tag the image with this name
                // .  = use Dockerfile in the current directory (WORKSPACE)
                sh "docker build -t ${FULL_IMAGE} ."

                echo "Successfully built: ${FULL_IMAGE}"
            }
        }
```

### Step 4 — Security scan with Trivy

```groovy
        stage('Security Scan') {
            steps {
                script {
                    // Run Trivy as a Docker container
                    // It will scan the image we just built
                    def scanResult = sh(
                        script: """
                            docker run --rm \
                                -v /var/run/docker.sock:/var/run/docker.sock \
                                aquasec/trivy:latest image \
                                --exit-code 1 \
                                --severity HIGH,CRITICAL \
                                --no-progress \
                                --format table \
                                ${FULL_IMAGE}
                        """,
                        returnStatus: true    // Don't fail immediately — capture exit code
                    )

                    if (scanResult != 0) {
                        // Trivy found HIGH or CRITICAL vulnerabilities
                        error("Security scan FAILED! Found HIGH or CRITICAL vulnerabilities in ${FULL_IMAGE}. Fix the vulnerabilities before pushing.")
                    }

                    echo "Security scan PASSED — no HIGH or CRITICAL vulnerabilities found."
                }
            }
        }
```

**Note on `returnStatus: true`:** Normally if a `sh` command fails (non-zero exit), Jenkins immediately fails the step. With `returnStatus: true`, Jenkins captures the exit code as a number instead. This lets us print a better error message before failing.

### Step 5 — Push to GHCR (only on main)

```groovy
        stage('Push to GHCR') {
            when {
                branch 'main'
                // Only push images built from the main branch
                // Feature branch builds are scanned but not pushed
            }
            steps {
                sh """
                    # Log in to GitHub Container Registry
                    # --password-stdin reads from stdin (pipe), not command args
                    # This prevents the token from appearing in process lists
                    echo ${GH_TOKEN} | docker login ghcr.io \
                        -u ${ORG} \
                        --password-stdin

                    # Push the versioned tag (abc12345-42)
                    docker push ${FULL_IMAGE}

                    # Also tag as 'latest' and push
                    # 'latest' is still useful as a convenience tag
                    # but the versioned tag is what matters for traceability
                    docker tag ${FULL_IMAGE} ${REGISTRY}/${ORG}/${APP}:latest
                    docker push ${REGISTRY}/${ORG}/${APP}:latest
                """
                echo "Image pushed successfully: ${FULL_IMAGE}"
            }
        }
```

---

### The Complete Final Solution

```groovy
pipeline {
    agent any

    environment {
        REGISTRY   = 'ghcr.io'
        ORG        = 'my-org'
        APP        = 'myapp'
        IMAGE_TAG  = "${GIT_COMMIT[0..7]}-${BUILD_NUMBER}"
        FULL_IMAGE = "${REGISTRY}/${ORG}/${APP}:${IMAGE_TAG}"
        GH_TOKEN   = credentials('github-container-token')
    }

    options {
        timestamps()
        timeout(time: 20, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                echo "Building image: ${FULL_IMAGE}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${FULL_IMAGE} ."
            }
        }

        stage('Security Scan') {
            steps {
                script {
                    echo "Scanning image for vulnerabilities..."

                    def exitCode = sh(
                        script: """
                            docker run --rm \
                                -v /var/run/docker.sock:/var/run/docker.sock \
                                aquasec/trivy:latest image \
                                --exit-code 1 \
                                --severity HIGH,CRITICAL \
                                --no-progress \
                                ${FULL_IMAGE}
                        """,
                        returnStatus: true
                    )

                    if (exitCode == 1) {
                        error("Image ${FULL_IMAGE} has HIGH/CRITICAL vulnerabilities. Not pushing to registry.")
                    }
                    echo "No HIGH or CRITICAL CVEs found. Image is safe to push."
                }
            }
        }

        stage('Push to GHCR') {
            when { branch 'main' }
            steps {
                sh """
                    echo ${GH_TOKEN} | docker login ${REGISTRY} \
                        -u ${ORG} --password-stdin
                    docker push ${FULL_IMAGE}
                    docker tag  ${FULL_IMAGE} ${REGISTRY}/${ORG}/${APP}:latest
                    docker push ${REGISTRY}/${ORG}/${APP}:latest
                """
            }
        }
    }

    post {
        success {
            script {
                if (env.BRANCH_NAME == 'main') {
                    slackSend color: 'good',
                              message: ":docker: Pushed ${FULL_IMAGE} to GHCR"
                }
            }
        }
        failure {
            slackSend color: 'danger',
                      message: ":x: Docker pipeline failed — ${env.JOB_NAME} #${env.BUILD_NUMBER}\n${env.BUILD_URL}"
        }
        always {
            // Clean up local Docker images to free disk space on the agent
            // || true = don't fail if image doesn't exist (e.g., build failed before docker build)
            sh "docker rmi ${FULL_IMAGE} || true"
            sh "docker rmi ${REGISTRY}/${ORG}/${APP}:latest || true"
            cleanWs()
        }
    }
}
```

---

## Visualizing the Flow

```
git push to feature/xyz:
  Checkout → Build Image → Security Scan → [Push SKIPPED — not main] → Cleanup

git push to main:
  Checkout → Build Image → Security Scan → Push to GHCR (versioned + latest) → Cleanup

If scan finds HIGH/CRITICAL CVE:
  Checkout → Build Image → Security Scan FAILS → Pipeline FAILS → No push happens
```

---

## Common Mistakes

### Mistake 1: Using `latest` only

```bash
# ❌ WRONG — no traceability
docker build -t myapp:latest .

# ✅ RIGHT — traceable tag + optional latest
docker build -t myapp:abc12345-42 .
docker tag myapp:abc12345-42 myapp:latest  # Latest is optional convenience tag
```

### Mistake 2: Password in command args

```bash
# ❌ WRONG — token visible in process list (ps aux | grep docker)
docker login -u myorg -p ghp_mytoken ghcr.io

# ✅ RIGHT — token piped via stdin
echo $GH_TOKEN | docker login ghcr.io -u myorg --password-stdin
```

### Mistake 3: Not cleaning up images

```groovy
// ❌ WRONG — images accumulate on agent, disk fills up after 100 builds
post {
    always { cleanWs() }  // cleanWs() only removes workspace files, NOT Docker images!
}

// ✅ RIGHT — explicitly remove Docker images
post {
    always {
        sh "docker rmi ${FULL_IMAGE} || true"
        cleanWs()
    }
}
```

### Mistake 4: Forgetting `--exit-code 1` on Trivy

```bash
# ❌ WRONG — Trivy finds critical CVEs but exits 0 (success) — pipeline passes!
docker run aquasec/trivy image myapp:latest

# ✅ RIGHT — exits 1 when vulnerabilities found, failing the Jenkins step
docker run aquasec/trivy image --exit-code 1 --severity HIGH,CRITICAL myapp:latest
```

---

## Summary

| Concept | Key detail |
|---------|-----------|
| Image tagging | Use `${GIT_COMMIT[0..7]}-${BUILD_NUMBER}` for full traceability |
| Trivy scanning | Must use `--exit-code 1` or findings are silently ignored |
| GHCR login | Use `echo $TOKEN \| docker login --password-stdin` (never `-p`) |
| Push gating | Use `when { branch 'main' }` to only push from main |
| Cleanup | `docker rmi ... \|\| true` prevents false failures in post block |


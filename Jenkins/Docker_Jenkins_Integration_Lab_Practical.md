# Docker and Jenkins Integration Lab Practical (DevOps)

# Aim

To understand Docker and Jenkins integration by building Docker images using Jenkins, running Docker inside Jenkins agents, using Docker plugins, publishing images to Docker Hub/GHCR, integrating Jenkins with GitHub, performing backup & restore, and implementing pipeline best practices.

---

# Software Requirements

- Docker Desktop
- Jenkins
- Git
- GitHub Account
- Docker Hub Account
- Java JDK 17
- Maven (Optional)

---

# Architecture Overview

Developer → GitHub Repository → Jenkins Pipeline → Docker Build → Docker Hub/GHCR → Deployment

---

# Step 1: Install Docker

## Verify Docker Installation

```bash
docker --version
```

## Expected Output

```bash
Docker version 26.x.x
```

---

# Step 2: Install Jenkins

## Start Jenkins

Open browser:

```text
http://localhost:8080
```

## Unlock Jenkins

Password location:

```text
C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword
```

---

# Step 3: Install Required Jenkins Plugins

Go to:

```text
Manage Jenkins → Plugins
```

## Install Plugins

- Docker Plugin
- Docker Pipeline Plugin
- Git Plugin
- GitHub Plugin
- Pipeline Plugin

Restart Jenkins after installation.

---

# Step 4: Configure Docker in Jenkins

## Open Global Tool Configuration

```text
Manage Jenkins → Tools
```

## Add Docker

```text
Name: Docker
Installation Path: Docker executable path
```

---

# Step 5: Allow Jenkins to Access Docker

## Windows

Add Jenkins user to Docker Desktop permissions.

Restart Jenkins service.

---

# Step 6: Create Sample Docker Application

## Project Structure

```text
docker-jenkins-app/
 ├── Dockerfile
 ├── app.py
 └── Jenkinsfile
```

---

# Step 7: Create Application File

## app.py

```python
print("Hello from Docker and Jenkins")
```

---

# Step 8: Create Dockerfile

## Dockerfile

```dockerfile
FROM python:3.11

WORKDIR /app

COPY app.py .

CMD ["python", "app.py"]
```

---

# Step 9: Test Docker Build Locally

## Build Docker Image

```bash
docker build -t docker-jenkins-app .
```

## Expected Output

```bash
Successfully built image
```

---

# Step 10: Run Docker Container

```bash
docker run docker-jenkins-app
```

## Expected Output

```bash
Hello from Docker and Jenkins
```

---

# Step 11: Push Project to GitHub

## Initialize Git Repository

```bash
git init
git add .
git commit -m "Initial commit"
```

## Connect GitHub Repository

```bash
git remote add origin https://github.com/your-username/docker-jenkins-app.git
git push -u origin main
```

---

# Step 12: Configure Docker Hub Credentials in Jenkins

Go to:

```text
Manage Jenkins → Credentials
```

## Add Credentials

```text
Username: Docker Hub Username
Password: Docker Hub Password/Token
ID: dockerhub-creds
```

---

# Step 13: Create Jenkins Pipeline Job

Go to:

```text
Dashboard → New Item → Pipeline
```

## Configure GitHub Repository

Add repository URL.

---

# Step 14: Create Jenkinsfile

## Jenkinsfile

```groovy
pipeline {

    agent any

    environment {
        IMAGE_NAME = "your-dockerhub-username/docker-jenkins-app"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/your-username/docker-jenkins-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Run Docker Container') {
            steps {
                bat 'docker run --rm %IMAGE_NAME%'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                bat 'docker push %IMAGE_NAME%'
            }
        }
    }
}
```

---

# Step 15: Run Jenkins Pipeline

Click:

```text
Build Now
```

---

# Step 16: Observe Console Output

## Build Output

```bash
Successfully built image
```

## Container Output

```bash
Hello from Docker and Jenkins
```

## Push Output

```bash
Pushed image successfully
```

---

# Step 17: Verify Docker Hub Image

Open Docker Hub.

## Expected Output

```text
Repository contains pushed Docker image
```

---

# Step 18: Publish Images to GitHub Container Registry (GHCR)

## Login to GHCR

```bash
docker login ghcr.io
```

## Tag Image

```bash
docker tag docker-jenkins-app ghcr.io/username/docker-jenkins-app
```

## Push Image

```bash
docker push ghcr.io/username/docker-jenkins-app
```

---

# Step 19: Jenkins and GitHub Integration

## Configure GitHub Webhook

Go to GitHub Repository:

```text
Settings → Webhooks
```

## Add Webhook URL

```text
http://your-jenkins-url/github-webhook/
```

## Select Event

```text
Just the push event
```

---

# Step 20: Automatic Build Trigger

Push code to GitHub:

```bash
git add .
git commit -m "Updated app"
git push
```

## Expected Output

```text
Jenkins pipeline triggered automatically
```

---

# Step 21: Docker Inside Jenkins Agents

## Example Agent Configuration

```groovy
pipeline {

    agent {
        docker {
            image 'python:3.11'
        }
    }

    stages {

        stage('Run Inside Container') {
            steps {
                sh 'python --version'
            }
        }
    }
}
```

---

# Step 22: Backup Jenkins

## Backup Jenkins Home

Backup folder:

```text
C:\ProgramData\Jenkins\.jenkins
```

## Backup Command

```bash
xcopy C:\ProgramData\Jenkins\.jenkins D:\jenkins-backup /E /I
```

---

# Step 23: Restore Jenkins

## Stop Jenkins Service

## Replace Jenkins Home Folder

Copy backup to original location.

## Restart Jenkins

---

# Step 24: Pipeline Best Practices

- Use Jenkinsfile in repository
- Use credentials securely
- Avoid hardcoding passwords
- Use stages properly
- Use reusable pipelines
- Clean unused Docker images
- Use version tags for Docker images

---

# Step 25: Common Docker Commands

| Command | Description |
|---|---|
| docker build | Build image |
| docker run | Run container |
| docker ps | List containers |
| docker images | List images |
| docker stop | Stop container |
| docker rm | Remove container |
| docker rmi | Remove image |

---

# Step 26: Troubleshooting

## Docker Command Not Found

### Solution

Add Docker path to environment variables.

---

## Jenkins Cannot Access Docker

### Solution

Restart Jenkins after Docker installation.

---

## Authentication Failed

### Solution

Verify Docker Hub credentials.

---

# Viva Questions

1. What is Docker?
2. What is Jenkins?
3. What is a Dockerfile?
4. What is a Jenkins Pipeline?
5. What is Docker Hub?
6. What is GHCR?
7. Why use Docker with Jenkins?
8. What is CI/CD?
9. What is a webhook?
10. Difference between image and container?

---

# Learning Outcomes

After completing this practical, students will be able to:

- Integrate Docker with Jenkins
- Build Docker images using Jenkins
- Push Docker images to Docker Hub/GHCR
- Configure Jenkins with GitHub
- Use Docker inside Jenkins agents
- Perform Jenkins backup and restore
- Implement pipeline best practices

---

# Conclusion

This practical demonstrated Docker and Jenkins integration in a DevOps workflow. Docker images were built and pushed using Jenkins pipelines, GitHub integration was configured for automatic builds, Docker containers were executed inside Jenkins agents, and Jenkins backup and restore procedures were performed successfully.

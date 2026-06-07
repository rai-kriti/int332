# Lab Exercise: Building Docker Images using Jenkins

## Subject
DevOps Laboratory

## Topic
Building Docker Images using Jenkins

---

# Aim

To learn how to integrate Docker with Jenkins and automatically build Docker images using Jenkins Pipeline.

---

# Learning Objectives

Students will learn:

- Docker and Jenkins integration
- Creating Docker images
- Jenkins Pipeline creation
- GitHub integration with Jenkins
- CI/CD workflow automation
- Running Docker containers

---

# Software Requirements

| Software | Purpose |
|---|---|
| Docker Desktop | Containerization platform |
| Jenkins | CI/CD automation |
| Git | Version control |
| Node.js | Sample application |
| VS Code | Code editor |

---

# Useful Official Websites

- Docker: https://www.docker.com
- Jenkins: https://www.jenkins.io
- GitHub: https://github.com
- Node.js: https://nodejs.org

---

# System Requirements

- Windows 10/11 or Linux
- Minimum 8 GB RAM
- Internet connection
- Administrator privileges

---

# PART 1 — CREATE SAMPLE APPLICATION

## Step 1: Create Project Folder

Open terminal or command prompt and run:

```bash
mkdir docker-jenkins-lab
cd docker-jenkins-lab
```

---

## Step 2: Initialize Node.js Project

Run:

```bash
npm init -y
```

This creates a file named:

```text
package.json
```

---

## Step 3: Create Application File

Create a file named:

```text
app.js
```

Add the following code:

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
    res.write("Docker Image Built Using Jenkins");
    res.end();
});

server.listen(3000, () => {
    console.log("Server running on port 3000");
});
```

---

# PART 2 — CREATE DOCKERFILE

## Step 4: Create Dockerfile

Create a file named:

```text
Dockerfile
```

Add the following code:

```dockerfile
FROM node:18

WORKDIR /app

COPY . .

EXPOSE 3000

CMD ["node", "app.js"]
```

---

# PART 3 — TEST DOCKER IMAGE LOCALLY

## Step 5: Build Docker Image

Run the command:

```bash
docker build -t docker-jenkins-lab .
```

Explanation:

- docker build → builds Docker image
- -t → gives image name
- . → current directory

---

## Step 6: Verify Docker Image

Run:

```bash
docker images
```

Expected Output:

```text
docker-jenkins-lab
```

---

## Step 7: Run Docker Container

Run:

```bash
docker run -d -p 3000:3000 docker-jenkins-lab
```

Explanation:

- -d → detached mode
- -p → port mapping

---

## Step 8: Test Application

Open browser:

```text
http://localhost:3000
```

Expected Output:

```text
Docker Image Built Using Jenkins
```

---

# PART 4 — PUSH PROJECT TO GITHUB

## Step 9: Initialize Git Repository

Run:

```bash
git init
git add .
git commit -m "Initial Docker Jenkins Lab"
```

---

## Step 10: Create GitHub Repository

Go to:

https://github.com/new

Repository Name:

```text
docker-jenkins-lab
```

---

## Step 11: Push Code to GitHub

Run:

```bash
git remote add origin https://github.com/USERNAME/docker-jenkins-lab.git
git branch -M main
git push -u origin main
```

Replace:

```text
USERNAME
```

with your GitHub username.

---

# PART 5 — INSTALL JENKINS

## Step 12: Open Jenkins

Open browser:

```text
http://localhost:8080
```

Unlock Jenkins using the initial admin password.

---

## Step 13: Install Suggested Plugins

Install:

- Git Plugin
- Pipeline Plugin
- Docker Pipeline Plugin

Restart Jenkins if required.

---

# PART 6 — VERIFY DOCKER WITH JENKINS

## Step 14: Verify Docker Installation

Run:

```bash
docker version
```

If Docker is properly installed, version details will appear.

---

## Step 15: Verify Jenkins Can Access Docker

### For Linux Users

Run:

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

### For Windows Users

- Start Docker Desktop
- Restart Jenkins service

---

# PART 7 — CREATE JENKINS PIPELINE

## Step 16: Create Jenkinsfile

Create a file named:

```text
Jenkinsfile
```

Add the following code:

```groovy
pipeline {

    agent any

    environment {
        IMAGE_NAME = "docker-jenkins-lab"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/USERNAME/docker-jenkins-lab.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Show Docker Images') {
            steps {
                bat 'docker images'
            }
        }
    }

    post {

        success {
            echo 'Docker image built successfully'
        }

        failure {
            echo 'Docker image build failed'
        }
    }
}
```

Replace:

```text
USERNAME
```

with your GitHub username.

---

# PART 8 — PUSH JENKINSFILE TO GITHUB

## Step 17: Commit and Push Jenkinsfile

Run:

```bash
git add Jenkinsfile
git commit -m "Added Jenkins pipeline"
git push
```

---

# PART 9 — CREATE PIPELINE JOB IN JENKINS

## Step 18: Create New Jenkins Job

1. Open Jenkins Dashboard
2. Click New Item
3. Enter job name:

```text
docker-build-job
```

4. Select:

```text
Pipeline
```

5. Click OK

---

## Step 19: Configure Pipeline

Under Pipeline Section:

| Setting | Value |
|---|---|
| Definition | Pipeline script from SCM |
| SCM | Git |
| Repository URL | GitHub repository URL |
| Branch | main |
| Script Path | Jenkinsfile |

Click Save.

---

# PART 10 — BUILD DOCKER IMAGE USING JENKINS

## Step 20: Run Pipeline

Click:

```text
Build Now
```

Observe pipeline stages:

1. Checkout Code
2. Build Docker Image
3. Show Docker Images

---

# PART 11 — VERIFY BUILD SUCCESS

## Step 21: Check Console Output

Open:

- Build History
- Latest Build
- Console Output

Expected message:

```text
Successfully tagged docker-jenkins-lab:latest
```

---

## Step 22: Verify Docker Image

Run:

```bash
docker images
```

Expected:

```text
docker-jenkins-lab
```

---

# PART 12 — RUN IMAGE CREATED BY JENKINS

## Step 23: Run Container

Run:

```bash
docker run -d -p 4000:3000 docker-jenkins-lab
```

---

## Step 24: Test Application

Open browser:

```text
http://localhost:4000
```

Expected Output:

```text
Docker Image Built Using Jenkins
```

---

# UNDERSTANDING PIPELINE STAGES

| Stage | Purpose |
|---|---|
| Checkout Code | Downloads source code from GitHub |
| Build Docker Image | Creates Docker image |
| Show Docker Images | Displays available images |
| Post Actions | Displays success/failure message |

---

# PRACTICE TASKS

## Task 1 — Modify Application Message

Change:

```javascript
res.write("Docker Image Built Using Jenkins");
```

to:

```javascript
res.write("Updated Docker Jenkins Pipeline");
```

Push changes and rebuild pipeline.

---

## Task 2 — Change Docker Image Name

Modify Jenkinsfile:

```groovy
IMAGE_NAME = "student-demo"
```

---

## Task 3 — Add New Pipeline Stage

Add:

```groovy
stage('Run Container') {
    steps {
        bat 'docker run -d -p 5000:3000 docker-jenkins-lab'
    }
}
```

---

## Task 4 — Create Intentional Failure

Modify Dockerfile:

```dockerfile
FROM unknownimage
```

Observe Jenkins failure logs.

---

# COMMON ERRORS AND SOLUTIONS

| Error | Solution |
|---|---|
| docker not recognized | Install Docker Desktop |
| Jenkins cannot access Docker | Restart Docker/Jenkins |
| Git checkout failed | Verify repository URL |
| Port already in use | Change port number |
| Pipeline failed | Check console logs |

---

# Viva Questions

1. What is Docker?
2. What is Jenkins?
3. What is CI/CD?
4. What is Dockerfile?
5. Difference between image and container?
6. What is Jenkins pipeline?
7. Why use Docker with Jenkins?
8. What is Jenkinsfile?
9. What does docker build command do?
10. What is the purpose of checkout stage?

---

# Expected Learning Outcomes

After completing this lab, students will be able to:

- Build Docker images using Jenkins
- Integrate GitHub with Jenkins
- Create Jenkins pipelines
- Automate Docker build process
- Understand CI/CD workflow
- Troubleshoot pipeline issues

---

# Conclusion

This practical demonstrated how Jenkins automates Docker image creation using CI/CD pipelines. Students learned real-world DevOps workflow including source code management, automated build process, and containerization.

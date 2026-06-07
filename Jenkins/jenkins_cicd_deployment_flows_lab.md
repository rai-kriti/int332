# Jenkins CI/CD Deployment Flows – DevOps Lab Exercises

## Lab Overview
This lab manual covers the following Jenkins CI/CD topics:

1. Triggering Builds using pollSCM
2. Triggering Builds using GitHub Webhooks
3. Jenkins Shared Pipeline Libraries
4. Jenkins Agents using SSH/SFTP
5. Container-Based Jenkins Agents
6. Deployments to Remote Servers
7. Deployments to Cloud Platforms

---

# Lab 1: Triggering Builds using pollSCM

## Objective
Learn how Jenkins automatically checks Git repositories for code changes using pollSCM.

## Commands
```bash
mkdir pollscm-demo
cd pollscm-demo

git init

echo "Hello Jenkins" > index.html

git add .
git commit -m "Initial Commit"
```

## pollSCM Schedule
```bash
H/2 * * * *
```

---

# Lab 2: Triggering Builds using GitHub Webhooks

## Webhook URL
```text
http://YOUR_JENKINS_URL/github-webhook/
```

## Jenkinsfile
```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Webhook Triggered Build'
            }
        }
    }
}
```

---

# Lab 3: Jenkins Shared Pipeline Libraries

## Shared Function
File: vars/buildApp.groovy

```groovy
def call() {
    echo 'Building Application using Shared Library'
}
```

## Jenkinsfile
```groovy
@Library('shared-library') _

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                buildApp()
            }
        }
    }
}
```

---

# Lab 4: Jenkins Agents using SSH/SFTP

## Install SSH Server
```bash
sudo apt update
sudo apt install openssh-server -y
```

## Create Jenkins User
```bash
sudo useradd jenkins
sudo passwd jenkins
```

## Agent Jenkinsfile
```groovy
pipeline {
    agent { label 'linux-agent' }

    stages {
        stage('Test') {
            steps {
                sh 'hostname'
            }
        }
    }
}
```

---

# Lab 5: Container-Based Jenkins Agents

## Install Docker
```bash
sudo apt update
sudo apt install docker.io -y
```

## Jenkinsfile
```groovy
pipeline {
    agent {
        docker {
            image 'maven:3.9.6-eclipse-temurin-17'
        }
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn --version'
            }
        }
    }
}
```

---

# Lab 6: Deployment to Remote Servers

## Deployment Script
```bash
#!/bin/bash

scp target/app.jar user@SERVER_IP:/home/user/

ssh user@SERVER_IP "java -jar /home/user/app.jar"
```

## Jenkinsfile
```groovy
pipeline {
    agent any

    stages {
        stage('Deploy') {
            steps {
                sh './deploy.sh'
            }
        }
    }
}
```

---

# Lab 7: Deployment to Cloud Platforms

## AWS Deployment
```groovy
pipeline {
    agent any

    stages {
        stage('Deploy to EC2') {
            steps {
                sh 'scp app.jar ubuntu@EC2-IP:/home/ubuntu/'
            }
        }
    }
}
```

## Docker Commands
```bash
docker build -t demo-app .
docker tag demo-app yourdockerhub/demo-app:v1
docker push yourdockerhub/demo-app:v1
```

---

# Viva Questions

1. What is pollSCM in Jenkins?
2. Difference between pollSCM and webhook?
3. What are Jenkins Shared Libraries?
4. Why are Jenkins agents used?
5. What is container-based build execution?
6. Difference between SCP and SFTP?
7. Why use Docker in CI/CD?
8. What are benefits of cloud deployment?
9. What is Jenkins Pipeline?
10. What is Infrastructure as Code?

---

# Learning Outcomes

After completing these labs, students will be able to:

- Configure Jenkins CI/CD pipelines.
- Trigger automated builds using SCM polling and webhooks.
- Use reusable shared pipeline libraries.
- Configure Jenkins agents.
- Run builds in Docker containers.
- Deploy applications to remote servers and cloud platforms.

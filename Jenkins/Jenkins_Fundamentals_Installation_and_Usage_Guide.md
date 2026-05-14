# Jenkins Fundamentals, Installation, and Usage Guide

**Prepared for beginners / layman-level learners**  
**Topic:** Jenkins Fundamentals, Installation, and Basic Usage  
**Purpose:** To help a completely new learner understand what Jenkins is, how to install it, and how to use it step by step.

---

## Table of Contents

1. [What is Jenkins?](#1-what-is-jenkins)
2. [Why Do We Need Jenkins?](#2-why-do-we-need-jenkins)
3. [What is CI/CD?](#3-what-is-cicd)
4. [Where Jenkins Fits in DevOps](#4-where-jenkins-fits-in-devops)
5. [Basic Jenkins Terminology](#5-basic-jenkins-terminology)
6. [Jenkins Architecture](#6-jenkins-architecture)
7. [System Requirements](#7-system-requirements)
8. [Install Java](#8-install-java)
9. [Install Jenkins on Windows](#9-install-jenkins-on-windows)
10. [Install Jenkins on Ubuntu/Linux](#10-install-jenkins-on-ubuntulinux)
11. [First-Time Jenkins Setup](#11-first-time-jenkins-setup)
12. [Understanding Jenkins Dashboard](#12-understanding-jenkins-dashboard)
13. [Creating Your First Freestyle Job](#13-creating-your-first-freestyle-job)
14. [Creating Your First Pipeline Job](#14-creating-your-first-pipeline-job)
15. [Jenkinsfile Explained](#15-jenkinsfile-explained)
16. [Connecting Jenkins with GitHub](#16-connecting-jenkins-with-github)
17. [Running a Simple Java/Maven Project in Jenkins](#17-running-a-simple-javamaven-project-in-jenkins)
18. [Running a Simple Node.js Project in Jenkins](#18-running-a-simple-nodejs-project-in-jenkins)
19. [Plugins in Jenkins](#19-plugins-in-jenkins)
20. [Managing Users and Security](#20-managing-users-and-security)
21. [Common Jenkins Errors and Solutions](#21-common-jenkins-errors-and-solutions)
22. [Best Practices](#22-best-practices)
23. [Quick Revision](#23-quick-revision)

---

# 1. What is Jenkins?

Jenkins is an open-source automation tool used mainly in software development. It helps developers automatically build, test, and deploy software.

In simple words:

> Jenkins is like an automatic helper that checks your project whenever you make changes.

For example, suppose a student is developing a website. Every time the student changes the code, Jenkins can automatically:

1. Download the latest code.
2. Check whether the code builds successfully.
3. Run tests.
4. Show success or failure.
5. Deploy the project if everything is correct.

Without Jenkins, a developer has to do all these steps manually. Jenkins saves time and reduces human mistakes.

---

# 2. Why Do We Need Jenkins?

Earlier, software teams followed a manual process:

1. Developer writes code.
2. Developer manually tests code.
3. Developer manually creates build/package.
4. Developer manually sends files to server.
5. If error occurs, the process is repeated manually.

This approach creates problems:

- It is slow.
- It is repetitive.
- It depends too much on humans.
- Mistakes are common.
- Bugs may be detected very late.

Jenkins solves these problems by automating the process.

## Real-Life Example

Think of Jenkins like a washing machine.

Without washing machine:

- You wash clothes manually.
- You rinse manually.
- You dry manually.

With washing machine:

- You put clothes inside.
- You press a button.
- The machine does the process automatically.

Similarly, Jenkins automatically builds, tests, and deploys software.

---

# 3. What is CI/CD?

Jenkins is mainly used for CI/CD.

## 3.1 CI: Continuous Integration

Continuous Integration means developers frequently merge their code into a common repository such as GitHub. After every code change, Jenkins automatically checks whether the project is still working or not.

Example:

- Developer pushes code to GitHub.
- Jenkins automatically starts.
- Jenkins builds the project.
- Jenkins runs tests.
- Jenkins reports success or failure.

## 3.2 CD: Continuous Delivery / Continuous Deployment

CD means automatically preparing or deploying the application after successful testing.

### Continuous Delivery

The application is automatically built and tested, but final deployment may require manual approval.

### Continuous Deployment

The application is automatically built, tested, and deployed without manual approval.

---

# 4. Where Jenkins Fits in DevOps

DevOps is a combination of development and operations. It focuses on faster and reliable software delivery.

Jenkins supports DevOps by automating the software delivery pipeline.

Typical DevOps flow:

```text
Developer writes code
        ↓
Code pushed to GitHub
        ↓
Jenkins detects change
        ↓
Jenkins builds project
        ↓
Jenkins runs tests
        ↓
Jenkins creates package
        ↓
Jenkins deploys application
```

---

# 5. Basic Jenkins Terminology

Before using Jenkins, we must understand some common terms.

## 5.1 Job

A Jenkins job is a task created inside Jenkins.

Example:

- Build Java project
- Run Python script
- Test Node.js application
- Deploy website

## 5.2 Build

A build is one execution/run of a Jenkins job.

Example:

If you click **Build Now**, Jenkins starts one build.

## 5.3 Workspace

Workspace is the folder where Jenkins keeps the project files while running a job.

## 5.4 Plugin

A plugin is an extra feature added to Jenkins.

Example:

- Git plugin helps Jenkins connect with Git repositories.
- Maven plugin helps Jenkins build Maven projects.
- Pipeline plugin helps Jenkins run pipeline scripts.

## 5.5 Pipeline

A pipeline is a sequence of steps used to build, test, and deploy an application.

Example:

```text
Checkout Code → Build → Test → Package → Deploy
```

## 5.6 Jenkinsfile

A Jenkinsfile is a text file that stores pipeline instructions.

It is usually saved inside the project repository.

---

# 6. Jenkins Architecture

Jenkins follows a master-agent architecture.

## 6.1 Jenkins Controller / Master

The Jenkins controller manages the main Jenkins system.

It performs tasks such as:

- Managing jobs
- Scheduling builds
- Managing users
- Showing dashboard
- Coordinating agents

## 6.2 Jenkins Agent / Node

An agent is a machine that actually runs the job.

Example:

- One agent may run Java builds.
- Another agent may run Python tests.
- Another agent may run Docker builds.

For beginner practice, Jenkins controller and agent can be the same computer.

Simple architecture:

```text
User Browser
     ↓
Jenkins Controller
     ↓
Build Agent
     ↓
Build / Test / Deploy
```

---

# 7. System Requirements

For basic Jenkins practice, you need:

- Windows 10/11 or Ubuntu/Linux
- Minimum 4 GB RAM
- Internet connection
- Java installed
- Web browser
- Optional: Git, Maven, Node.js, Docker

Important:

> Jenkins requires Java to run.

---

# 8. Install Java

Jenkins needs Java. Current Jenkins versions generally require Java 17 or Java 21.

## 8.1 Check Whether Java is Already Installed

Open Command Prompt or Terminal and run:

```bash
java -version
```

If Java is installed, you will see output like:

```text
java version "17.x.x"
```

If Java is not installed, install it first.

## 8.2 Install Java on Windows

### Step 1: Download Java JDK

Download and install **Eclipse Temurin JDK 17** or **Oracle JDK 17**.

Recommended for beginners:

```text
Eclipse Temurin JDK 17
```

### Step 2: Install Java

Run the downloaded installer and complete installation.

### Step 3: Verify Java

Open Command Prompt and run:

```bash
java -version
```

Expected output:

```text
openjdk version "17.x.x"
```

## 8.3 Install Java on Ubuntu/Linux

Run these commands:

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
java -version
```

---

# 9. Install Jenkins on Windows

## Step 1: Download Jenkins

Go to the official Jenkins website and download the Windows installer.

Choose:

```text
Windows installer package
```

## Step 2: Run the Installer

Double-click the downloaded Jenkins `.msi` file.

Click:

```text
Next → Next → Install
```

## Step 3: Select Jenkins Service User

During installation, Jenkins may ask for service login type.

For beginner practice, select:

```text
Run service as LocalSystem
```

## Step 4: Select Java Path

The installer may automatically detect Java.

If not, provide the Java installation path manually.

Example path:

```text
C:\Program Files\Java\jdk-17
```

## Step 5: Complete Installation

After installation, Jenkins will start automatically.

Open browser and visit:

```text
http://localhost:8080
```

If Jenkins opens, installation is successful.

---

# 10. Install Jenkins on Ubuntu/Linux

## Step 1: Update System

```bash
sudo apt update
```

## Step 2: Install Java

```bash
sudo apt install openjdk-17-jdk -y
```

Verify Java:

```bash
java -version
```

## Step 3: Add Jenkins Repository Key

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```

## Step 4: Add Jenkins Repository

```bash
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

## Step 5: Install Jenkins

```bash
sudo apt update
sudo apt install jenkins -y
```

## Step 6: Start Jenkins

```bash
sudo systemctl start jenkins
```

## Step 7: Enable Jenkins on Boot

```bash
sudo systemctl enable jenkins
```

## Step 8: Check Jenkins Status

```bash
sudo systemctl status jenkins
```

If it shows `active running`, Jenkins is working.

## Step 9: Open Jenkins in Browser

Open:

```text
http://localhost:8080
```

If Jenkins is installed on a remote Linux server, use:

```text
http://server-ip-address:8080
```

---

# 11. First-Time Jenkins Setup

When Jenkins opens for the first time, it asks for an administrator password.

## 11.1 Get Initial Admin Password on Windows

Go to this file:

```text
C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword
```

Open the file and copy the password.

## 11.2 Get Initial Admin Password on Ubuntu/Linux

Run:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the password.

## 11.3 Unlock Jenkins

Paste the password in the Jenkins browser screen and click:

```text
Continue
```

## 11.4 Install Suggested Plugins

Jenkins will show two options:

1. Install suggested plugins
2. Select plugins to install

For beginners, choose:

```text
Install suggested plugins
```

## 11.5 Create First Admin User

Fill the details:

```text
Username: admin
Password: your-password
Full Name: Your Name
Email: your-email@example.com
```

Click:

```text
Save and Continue
```

## 11.6 Jenkins URL

Keep the default:

```text
http://localhost:8080
```

Click:

```text
Save and Finish
```

Now Jenkins is ready.

---

# 12. Understanding Jenkins Dashboard

After login, you will see the Jenkins dashboard.

Important options:

## 12.1 New Item

Used to create a new Jenkins job.

## 12.2 Manage Jenkins

Used for configuration, plugins, tools, security, and system settings.

## 12.3 Build History

Shows previous job runs.

## 12.4 People

Shows Jenkins users.

## 12.5 Credentials

Used to store usernames, passwords, tokens, SSH keys, etc.

---

# 13. Creating Your First Freestyle Job

A freestyle job is the simplest type of Jenkins job.

## Objective

We will create a job that prints a simple message.

## Step 1: Click New Item

From Jenkins dashboard, click:

```text
New Item
```

## Step 2: Enter Job Name

Enter:

```text
first-freestyle-job
```

## Step 3: Select Freestyle Project

Choose:

```text
Freestyle project
```

Click:

```text
OK
```

## Step 4: Add Build Step

Scroll down to **Build Steps**.

Click:

```text
Add build step
```

On Windows, choose:

```text
Execute Windows batch command
```

On Linux, choose:

```text
Execute shell
```

## Step 5: Add Command

For Windows:

```bat
echo Hello Jenkins
echo This is my first Jenkins job
```

For Linux:

```bash
echo "Hello Jenkins"
echo "This is my first Jenkins job"
```

## Step 6: Save Job

Click:

```text
Save
```

## Step 7: Run Job

Click:

```text
Build Now
```

## Step 8: Check Output

Click the build number under **Build History**.

Then click:

```text
Console Output
```

You should see:

```text
Hello Jenkins
This is my first Jenkins job
```

Congratulations! You have created and executed your first Jenkins job.

---

# 14. Creating Your First Pipeline Job

A pipeline job is more powerful than a freestyle job. It allows us to define multiple stages.

## Step 1: Create New Item

Click:

```text
New Item
```

Enter name:

```text
first-pipeline-job
```

Select:

```text
Pipeline
```

Click:

```text
OK
```

## Step 2: Add Pipeline Script

Scroll down to the **Pipeline** section.

Select:

```text
Pipeline script
```

Paste this code:

```groovy
pipeline {
    agent any

    stages {
        stage('Welcome') {
            steps {
                echo 'Welcome to Jenkins Pipeline'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing the project...'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying the project...'
            }
        }
    }
}
```

## Step 3: Save

Click:

```text
Save
```

## Step 4: Run Pipeline

Click:

```text
Build Now
```

## Step 5: View Result

Open **Console Output**.

You will see each stage running one by one.

---

# 15. Jenkinsfile Explained

A Jenkinsfile stores pipeline code inside the project repository.

Example Jenkinsfile:

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Downloading code from repository'
            }
        }

        stage('Build') {
            steps {
                echo 'Building application'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application'
            }
        }
    }
}
```

## Explanation

### pipeline

This is the main block of Jenkins pipeline.

### agent any

This means Jenkins can run the job on any available machine.

### stages

Stages represent major parts of the pipeline.

Example:

- Checkout
- Build
- Test
- Deploy

### stage

Each stage contains a specific task.

### steps

Steps are actual commands executed inside a stage.

---

# 16. Connecting Jenkins with GitHub

Jenkins can automatically download code from GitHub.

## 16.1 Install Git

First install Git on your system.

Check Git:

```bash
git --version
```

If Git is installed, it will show version.

## 16.2 Install Git Plugin in Jenkins

Go to:

```text
Manage Jenkins → Plugins → Available Plugins
```

Search:

```text
Git
```

Install Git plugin if not already installed.

## 16.3 Create a GitHub Repository

Example repository:

```text
https://github.com/your-username/jenkins-demo.git
```

## 16.4 Create a Pipeline Job from GitHub

Create a new pipeline job.

In Pipeline section, choose:

```text
Pipeline script from SCM
```

Select SCM:

```text
Git
```

Enter repository URL:

```text
https://github.com/your-username/jenkins-demo.git
```

Script path:

```text
Jenkinsfile
```

Save and run the job.

---

# 17. Running a Simple Java/Maven Project in Jenkins

## 17.1 What is Maven?

Maven is a build tool for Java projects. It helps compile code, run tests, and create `.jar` files.

## 17.2 Install Maven

Check Maven:

```bash
mvn -version
```

If Maven is not installed, install it.

### Ubuntu/Linux

```bash
sudo apt install maven -y
```

### Windows

Download Maven, extract it, and add Maven `bin` folder to system PATH.

## 17.3 Sample Maven Project Structure

```text
java-maven-demo/
│
├── pom.xml
└── src/
    ├── main/
    │   └── java/
    │       └── App.java
    └── test/
        └── java/
            └── AppTest.java
```

## 17.4 Jenkinsfile for Maven Project

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }
}
```

For Windows Jenkins, use `bat` instead of `sh`:

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }
    }
}
```

---

# 18. Running a Simple Node.js Project in Jenkins

## 18.1 Install Node.js

Check Node.js:

```bash
node -v
npm -v
```

If not installed, install Node.js from the official website.

## 18.2 Sample Node.js Project Structure

```text
node-demo/
│
├── package.json
├── app.js
├── test.js
└── Jenkinsfile
```

## 18.3 Sample package.json

```json
{
  "name": "node-demo",
  "version": "1.0.0",
  "scripts": {
    "test": "node test.js",
    "start": "node app.js"
  }
}
```

## 18.4 Sample app.js

```javascript
function add(a, b) {
    return a + b;
}

module.exports = add;
```

## 18.5 Sample test.js

```javascript
const add = require('./app');

if (add(2, 3) === 5) {
    console.log('Test passed');
} else {
    console.log('Test failed');
    process.exit(1);
}
```

## 18.6 Jenkinsfile for Node.js Project

For Linux:

```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
    }
}
```

For Windows:

```groovy
pipeline {
    agent any

    stages {
        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                bat 'npm test'
            }
        }
    }
}
```

---

# 19. Plugins in Jenkins

Plugins extend Jenkins functionality.

## Common Jenkins Plugins

| Plugin | Purpose |
|---|---|
| Git Plugin | Connect Jenkins with Git repositories |
| Pipeline Plugin | Create pipeline jobs |
| Maven Integration Plugin | Build Maven projects |
| Docker Plugin | Work with Docker containers |
| GitHub Integration Plugin | Connect GitHub webhooks |
| Credentials Plugin | Manage secrets securely |
| Blue Ocean | Modern pipeline visualization |

## How to Install Plugins

Go to:

```text
Manage Jenkins → Plugins → Available Plugins
```

Search plugin name.

Select plugin.

Click:

```text
Install
```

Restart Jenkins if required.

---

# 20. Managing Users and Security

Security is important in Jenkins because Jenkins can access source code, credentials, and servers.

## 20.1 Create User

Go to:

```text
Manage Jenkins → Users → Create User
```

Enter user details and save.

## 20.2 Manage Security

Go to:

```text
Manage Jenkins → Security
```

Recommended beginner settings:

- Enable security
- Use Jenkins own user database
- Allow users to sign up: unchecked
- Authorization: Logged-in users can do anything

For production, use role-based access control.

---

# 21. Common Jenkins Errors and Solutions

## Error 1: Jenkins Page Not Opening

Problem:

```text
http://localhost:8080 not opening
```

Solution:

Check Jenkins service.

Windows:

```text
Open Services → Find Jenkins → Start
```

Linux:

```bash
sudo systemctl status jenkins
sudo systemctl start jenkins
```

## Error 2: Port 8080 Already in Use

Problem:

Another application is using port 8080.

Solution:

Change Jenkins port.

On Linux, edit:

```bash
sudo nano /etc/default/jenkins
```

Change port from 8080 to another port, such as 9090.

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

## Error 3: Java Not Found

Problem:

```text
java is not recognized
```

Solution:

Install Java and set PATH properly.

Verify:

```bash
java -version
```

## Error 4: Git Not Found

Problem:

```text
git is not recognized
```

Solution:

Install Git and add it to PATH.

Verify:

```bash
git --version
```

## Error 5: Maven Not Found

Problem:

```text
mvn is not recognized
```

Solution:

Install Maven and add Maven `bin` folder to PATH.

Verify:

```bash
mvn -version
```

## Error 6: npm PowerShell Script Disabled on Windows

Problem:

```text
npm.ps1 cannot be loaded because running scripts is disabled
```

Simple solution:

Use Command Prompt instead of PowerShell.

Or run this in PowerShell as administrator:

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Then try again.

---

# 22. Best Practices

1. Use Jenkinsfile instead of manually configuring everything in UI.
2. Store Jenkinsfile with project code in GitHub.
3. Do not store passwords directly in pipeline code.
4. Use Jenkins Credentials Manager for secrets.
5. Keep Jenkins plugins updated.
6. Use separate stages for build, test, and deploy.
7. Check console output carefully when a job fails.
8. Use meaningful job names.
9. Keep backup of Jenkins configuration.
10. Use agents for large projects instead of running everything on controller.

---

# 23. Quick Revision

## What is Jenkins?

Jenkins is an automation tool used for CI/CD.

## Why is Jenkins used?

It automatically builds, tests, and deploys software.

## What is a job?

A job is a task created in Jenkins.

## What is a build?

A build is one execution of a job.

## What is a pipeline?

A pipeline is a sequence of stages such as build, test, and deploy.

## What is Jenkinsfile?

A Jenkinsfile is a file that stores pipeline instructions.

## What is a plugin?

A plugin adds extra functionality to Jenkins.

## What is CI?

Continuous Integration means automatically checking code whenever developers push changes.

## What is CD?

Continuous Delivery or Deployment means automatically preparing or deploying the application.

---

# Final Beginner Workflow

For a new learner, follow this sequence:

```text
Step 1: Install Java
Step 2: Install Jenkins
Step 3: Open Jenkins at http://localhost:8080
Step 4: Unlock Jenkins using initial admin password
Step 5: Install suggested plugins
Step 6: Create admin user
Step 7: Create first freestyle job
Step 8: Run job and check console output
Step 9: Create first pipeline job
Step 10: Learn Jenkinsfile
Step 11: Connect Jenkins with GitHub
Step 12: Automate build and test process
```

---

# Simple Diagram

```text
Developer
   |
   | Push Code
   v
GitHub Repository
   |
   | Jenkins Pulls Code
   v
Jenkins Pipeline
   |
   | Build → Test → Package → Deploy
   v
Application Ready
```

---

# Conclusion

Jenkins is one of the most important tools in DevOps. It helps automate repetitive software development tasks. A beginner should first understand jobs, builds, plugins, pipelines, and Jenkinsfile. After that, the learner should practice by creating simple freestyle and pipeline jobs. Once comfortable, Jenkins can be connected with GitHub, Maven, Node.js, Docker, and deployment servers.


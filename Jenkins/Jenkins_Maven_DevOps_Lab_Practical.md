# Jenkins and Maven Lab Practical (DevOps)

## Aim
To understand Maven installation in Jenkins, configure Maven globally, run Maven builds in Jenkins Pipelines, and generate code coverage and test reports.

---

# Software Requirements

- Java JDK 17 or above
- Apache Maven
- Jenkins
- Git
- Maven Project

---

# Architecture Overview

Developer → GitHub Repository → Jenkins Pipeline → Maven Build → Test Reports → Code Coverage

---

# Step 1: Install Java

## Windows Command
```bash
java -version
```

## Expected Output
```bash
java version "17.0.8"
Java(TM) SE Runtime Environment
```

---

# Step 2: Install Apache Maven

## Download Maven
Download Maven from:
https://maven.apache.org/download.cgi

## Extract Maven
Example path:
```text
C:\Program Files\Apache\maven
```

## Set Environment Variables

### Add MAVEN_HOME
```text
MAVEN_HOME=C:\Program Files\Apache\maven
```

### Add Path Variable
```text
%MAVEN_HOME%\bin
```

## Verify Installation
```bash
mvn -version
```

## Expected Output
```bash
Apache Maven 3.9.x
Java version: 17
```

---

# Step 3: Install Jenkins

## Start Jenkins
Open:
```text
http://localhost:8080
```

## Unlock Jenkins
Use password from:
```text
C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword
```

## Install Suggested Plugins

Install:
- Git Plugin
- Maven Integration Plugin
- Pipeline Plugin
- JUnit Plugin
- JaCoCo Plugin

---

# Step 4: Configure Maven in Jenkins

## Open Global Tool Configuration

Go to:
```text
Manage Jenkins → Tools
```

## Configure JDK

### Add JDK
```text
Name: JDK17
JAVA_HOME: C:\Program Files\Java\jdk-17
```

## Configure Maven

### Add Maven
```text
Name: Maven3
MAVEN_HOME: C:\Program Files\Apache\maven
```

## Save Configuration

---

# Step 5: Create Maven Project

## Create Project Structure
```text
sample-maven-app/
 ├── src/
 ├── pom.xml
 └── Jenkinsfile
```

---

# Step 6: Create pom.xml

## pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.devops</groupId>
    <artifactId>sample-app</artifactId>
    <version>1.0</version>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>

            <plugin>
                <groupId>org.jacoco</groupId>
                <artifactId>jacoco-maven-plugin</artifactId>
                <version>0.8.8</version>

                <executions>

                    <execution>
                        <goals>
                            <goal>prepare-agent</goal>
                        </goals>
                    </execution>

                    <execution>
                        <id>report</id>
                        <phase>test</phase>

                        <goals>
                            <goal>report</goal>
                        </goals>
                    </execution>

                </executions>
            </plugin>

        </plugins>
    </build>

</project>
```

---

# Step 7: Create Jenkins Pipeline

## Create Pipeline Job

Go to:
```text
Dashboard → New Item → Pipeline
```

## Configure GitHub Repository
Add repository URL.

---

# Step 8: Create Jenkinsfile

## Jenkinsfile
```groovy
pipeline {

    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo/sample-maven-app.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Package') {
            steps {
                bat 'mvn package'
            }
        }

    }

    post {

        always {

            junit '**/target/surefire-reports/*.xml'

            jacoco(
                execPattern: 'target/jacoco.exec',
                classPattern: 'target/classes',
                sourcePattern: 'src/main/java'
            )
        }
    }
}
```

---

# Step 9: Run Jenkins Pipeline

## Build Pipeline
Click:
```text
Build Now
```

---

# Step 10: Observe Console Output

## Build Stage Output
```bash
[INFO] BUILD SUCCESS
```

## Test Stage Output
```bash
Tests run: 1, Failures: 0, Errors: 0
```

## Package Stage Output
```bash
Building jar: target/sample-app-1.0.jar
```

---

# Step 11: View Test Reports

Go to:
```text
Pipeline Job → Latest Build → Test Result
```

## Expected Output
```text
Total Tests: 1
Failures: 0
```

---

# Step 12: View Code Coverage Report

Go to:
```text
Pipeline Job → Latest Build → Code Coverage
```

## Expected Output
```text
Instruction Coverage: 85%
Branch Coverage: 80%
```

---

# Step 13: Common Maven Commands

| Command | Description |
|---|---|
| mvn clean | Removes previous build files |
| mvn compile | Compiles source code |
| mvn test | Runs test cases |
| mvn package | Creates JAR/WAR |
| mvn install | Installs package locally |

---

# Step 14: Troubleshooting

## Maven Not Recognized
### Solution
Check PATH variable:
```text
%MAVEN_HOME%\bin
```

---

## Jenkins Build Failed
### Solution
- Verify JDK path
- Verify Maven path
- Check Jenkins plugins
- Verify Git repository URL

---

# Viva Questions

1. What is Maven?
2. What is Jenkins?
3. What is a Jenkins Pipeline?
4. What is pom.xml?
5. What is code coverage?
6. What is JaCoCo?
7. Difference between compile and package?
8. Why use JUnit?

---

# Learning Outcomes

After completing this practical, students will be able to:
- Install Maven in Jenkins
- Configure Maven globally
- Create Jenkins pipelines
- Run Maven builds
- Generate test reports
- Generate code coverage reports

---

# Conclusion

This practical demonstrated the integration of Jenkins and Maven in a DevOps environment. Maven was configured in Jenkins using Global Tool Configuration, and Maven builds were executed using Jenkins Pipelines. JUnit test reports and JaCoCo code coverage reports were generated successfully.


# Maven Build Automation — Detailed Beginner Guide

This guide explains **Maven Build Automation** in a very detailed and beginner-friendly way, as if a completely new person is reading it.

---

# 1. What is Maven?

Maven is a **build automation tool** mainly used for Java projects.

In simple words, Maven helps you:
- compile Java code
- run tests
- download libraries automatically
- create JAR/WAR files
- install projects on your local system
- deploy projects to remote repositories
- integrate Java projects with Docker and CI/CD tools

Instead of doing many tasks manually, Maven does them in a proper sequence using commands.

---

# 2. Why build tools exist

Imagine you are making a Java project without Maven.

You would need to:
- compile `.java` files manually
- remember all commands
- download required libraries yourself
- set classpaths manually
- run tests manually
- create JAR files manually
- copy files to correct folders manually

This becomes difficult, confusing, and error-prone.

That is why build tools like Maven exist.

## Build tools solve these problems

Build tools help by:
- automating repeated tasks
- making builds consistent for all developers
- reducing human mistakes
- downloading dependencies automatically
- organizing project structure
- running tests in a standard way
- packaging the application properly
- helping teams work in a uniform manner

So, Maven is like a **smart helper** that follows instructions written in one file called `pom.xml`.

---

# 3. Problems solved by automated builds

Automated builds solve many real-world problems.

## Problem 1: Manual compilation is difficult
If a project has many Java files, compiling each file manually is hard.

## Problem 2: Dependency management is messy
If your project needs external libraries such as JUnit or MySQL connector, downloading them one by one is confusing.

## Problem 3: Builds differ from one system to another
One developer may compile in one way, another may do it differently.

## Problem 4: Tests may be forgotten
A developer may forget to run tests before packaging.

## Problem 5: Packaging may be inconsistent
One person may create a normal JAR, another may miss required libraries.

## Problem 6: Deployment becomes complicated
Sending artifacts manually to a server or repository is slow and error-prone.

Maven helps solve all of these.

---

# 4. Main idea of Maven

Maven works mainly using three things:

## 4.1 POM
POM means **Project Object Model**.  
It is the main XML file of Maven project called `pom.xml`.

## 4.2 Lifecycle
Lifecycle means a fixed sequence of steps such as:
- validate
- compile
- test
- package
- verify
- install
- deploy

## 4.3 Plugins
Plugins are tools that actually perform work, such as:
- compiling code
- running tests
- creating JAR files
- creating uber JAR
- integrating Docker

---

# 5. Standard Maven directory structure

A Maven project follows a standard structure.

```text
maven-automation-demo/
├── pom.xml
├── Dockerfile
├── .mvn/
│   └── wrapper/
├── mvnw
├── mvnw.cmd
└── src/
    ├── main/
    │   └── java/
    │       └── com/
    │           └── example/
    │               └── App.java
    └── test/
        └── java/
            └── com/
                └── example/
                    └── AppTest.java
```

## Explanation of each folder

### `pom.xml`
This is the main Maven file. It tells Maven everything about the project.

### `src/main/java`
This folder contains actual application Java code.

### `src/test/java`
This folder contains test code.

### `target`
This folder is created by Maven automatically during build.  
It stores:
- compiled classes
- test reports
- generated JAR files
- temporary build files

### `.mvn`, `mvnw`, `mvnw.cmd`
These are used for Maven Wrapper.

---

# 6. Project Object Model (POM)

The `pom.xml` file is the heart of Maven.

It tells Maven:
- project name
- project version
- packaging type
- dependencies
- plugins
- Java version
- repositories
- deployment configuration

## Example `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>maven-automation-demo</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>
    <name>Maven Automation Demo</name>

    <properties>
        <maven.compiler.release>21</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <junit.version>5.10.2</junit.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.15.0</version>
                <configuration>
                    <release>21</release>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.5.5</version>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.6.2</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>com.example.App</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

            <plugin>
                <groupId>com.spotify</groupId>
                <artifactId>dockerfile-maven-plugin</artifactId>
                <configuration>
                    <repository>yourdockerhubusername/maven-automation-demo</repository>
                    <tag>${project.version}</tag>
                    <buildArgs>
                        <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
                    </buildArgs>
                </configuration>
                <executions>
                    <execution>
                        <id>default</id>
                        <goals>
                            <goal>build</goal>
                            <goal>tag</goal>
                            <goal>push</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

    <distributionManagement>
        <repository>
            <id>company-releases</id>
            <url>https://repo.example.com/releases</url>
        </repository>
    </distributionManagement>

</project>
```

---

# 7. Meaning of important POM tags

## `<modelVersion>`
Tells which Maven POM model version is used.

## `<groupId>`
Usually represents organization or package group.

Example:
```xml
<groupId>com.example</groupId>
```

## `<artifactId>`
Project name.

Example:
```xml
<artifactId>maven-automation-demo</artifactId>
```

## `<version>`
Project version.

Example:
```xml
<version>1.0.0</version>
```

## `<packaging>`
Type of output generated.

Common values:
- `jar`
- `war`
- `pom`

## `<properties>`
Stores reusable values such as Java version and dependency version.

## `<dependencies>`
Libraries needed by project.

## `<build>`
Contains build settings.

## `<plugins>`
Contains plugin definitions.

## `<distributionManagement>`
Tells where built artifact should be deployed.

---

# 8. Java application code

Create this file:

`src/main/java/com/example/App.java`

```java
package com.example;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello from Maven!");
        System.out.println("2 + 3 = " + add(2, 3));
    }

    public static int add(int a, int b) {
        return a + b;
    }
}
```

## Explanation
- `main()` is the starting point of Java program.
- `add()` is a simple method for demonstration.
- Maven will compile this file during the `compile` phase.

---

# 9. Test code

Create this file:

`src/test/java/com/example/AppTest.java`

```java
package com.example;

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class AppTest {

    @Test
    void testAdd() {
        assertEquals(5, App.add(2, 3));
    }
}
```

## Explanation
- This file is used to test application logic.
- `assertEquals(5, App.add(2, 3))` checks whether method returns correct result.
- Maven runs this using Surefire plugin during `test` phase.

---

# 10. Maven lifecycle phases

Maven follows a sequence called **lifecycle**.

The main lifecycle phases are:

1. validate
2. compile
3. test
4. package
5. verify
6. install
7. deploy

Important rule:

If you run a later phase, Maven automatically runs all earlier phases also.

Example:
```bash
mvn package
```

This automatically performs:
- validate
- compile
- test
- package

---

# 11. Detailed explanation of each lifecycle phase

## 11.1 validate

Command:
```bash
mvn validate
```

### What it does
- checks project structure
- checks whether `pom.xml` is valid
- checks whether required information is available

### Beginner meaning
This is like checking whether all required things are present before starting actual work.

---

## 11.2 compile

Command:
```bash
mvn compile
```

### What it does
- compiles files from `src/main/java`
- creates `.class` files
- stores them in `target/classes`

### Beginner meaning
Java source code is converted into bytecode which JVM can understand.

---

## 11.3 test

Command:
```bash
mvn test
```

### What it does
- compiles test code from `src/test/java`
- runs tests
- creates test reports

### Beginner meaning
It checks whether your program works correctly.

---

## 11.4 package

Command:
```bash
mvn package
```

### What it does
- takes compiled code
- creates output file such as JAR
- stores it in `target`

### Beginner meaning
This creates a file that can be shared or run later.

---

## 11.5 verify

Command:
```bash
mvn verify
```

### What it does
- performs checks on package
- ensures build is valid

### Beginner meaning
This is like a final quality check before installing or deploying.

---

## 11.6 install

Command:
```bash
mvn install
```

### What it does
- copies built artifact into local Maven repository

Usually local repository path is something like:

```text
C:\Users\YourName\.m2\repository
```

### Beginner meaning
Now this project can be used as dependency in other local projects on your computer.

---

## 11.7 deploy

Command:
```bash
mvn deploy
```

### What it does
- uploads artifact to remote Maven repository

### Beginner meaning
Now other people or servers can download your built project.

---

# 12. Try all lifecycle commands in order

Run these commands from project folder:

```bash
mvn validate
mvn compile
mvn test
mvn package
mvn verify
mvn install
```

Later, if remote repository is configured:

```bash
mvn deploy
```

---

# 13. Dependency scope

Dependency scope tells Maven **when** dependency is needed.

## Common scopes

### 13.1 compile
Default scope. Needed everywhere.

### 13.2 provided
Needed for compile, but expected to be provided by runtime environment.

Example:
- servlet-api in web server

### 13.3 runtime
Not needed for compile but needed while running application.

### 13.4 test
Needed only during testing.

---

# 14. Example of dependency scopes

```xml
<dependencies>
    <dependency>
        <groupId>com.mysql</groupId>
        <artifactId>mysql-connector-j</artifactId>
        <version>9.0.0</version>
        <scope>runtime</scope>
    </dependency>

    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.10.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

## Explanation
- MySQL connector is needed while application runs
- JUnit is only needed when running tests

---

# 15. Transitive dependencies

Transitive dependencies are indirect dependencies.

## Example
Suppose:
- your project depends on library A
- library A depends on library B

Then Maven may download library B automatically.

So:
- A is direct dependency
- B is transitive dependency

## Command to see dependency tree

```bash
mvn dependency:tree
```

This helps you understand:
- which libraries are added
- from where they are coming
- which version is being selected

---

# 16. Version conflicts and resolution

Sometimes two dependencies bring different versions of same library.

Example:
- dependency X brings library Z version 1.0
- dependency Y brings library Z version 2.0

Then conflict happens.

## How Maven resolves it
Maven usually uses:
- nearest definition wins
- if same depth, first declared wins

## How to solve manually
You can explicitly define correct version in your own `pom.xml`.

---

# 17. Dependency management

`dependencyManagement` is used to control versions centrally.

It is especially useful in:
- multi-module projects
- parent-child projects
- large enterprise projects

## Example

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.junit</groupId>
            <artifactId>junit-bom</artifactId>
            <version>5.10.2</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

## Why useful
Instead of writing version repeatedly in every module, you control it in one place.

---

# 18. Parent POM

A Parent POM is used to define common configuration for multiple child projects.

## Parent POM example

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>parent-project</artifactId>
    <version>1.0.0</version>
    <packaging>pom</packaging>

    <modules>
        <module>service-a</module>
        <module>service-b</module>
    </modules>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.junit.jupiter</groupId>
                <artifactId>junit-jupiter-api</artifactId>
                <version>5.10.2</version>
                <scope>test</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

## Child POM example

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.example</groupId>
        <artifactId>parent-project</artifactId>
        <version>1.0.0</version>
        <relativePath>../pom.xml</relativePath>
    </parent>

    <artifactId>service-a</artifactId>
</project>
```

## Beginner meaning
Parent POM is like a main controller.  
Child projects inherit common settings from parent.

---

# 19. Maven plugins

Plugins are the actual workers of Maven.

Without plugins, Maven cannot do much.

Important plugins here are:
- Compiler plugin
- Surefire plugin
- Shade plugin
- Docker plugin

---

# 20. Compiler plugin

Used to compile Java source code.

## Example

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.15.0</version>
    <configuration>
        <release>21</release>
    </configuration>
</plugin>
```

## Explanation
- tells Maven to compile Java code
- sets Java release version to 21

---

# 21. Surefire plugin

Used to run unit tests.

## Example

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.5.5</version>
</plugin>
```

## Explanation
- runs tests from `src/test/java`
- creates test reports
- helps validate correctness of application

---

# 22. Shade plugin

Used to create **uber jar**.

Uber jar means:
- one single JAR file
- includes your code
- includes required dependencies

## Example

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>3.6.2</version>
    <executions>
        <execution>
            <phase>package</phase>
            <goals>
                <goal>shade</goal>
            </goals>
            <configuration>
                <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>com.example.App</mainClass>
                    </transformer>
                </transformers>
            </configuration>
        </execution>
    </executions>
</plugin>
```

## Explanation
- runs during `package`
- creates bigger JAR containing dependencies
- defines main class so JAR can be run easily

---

# 23. Maven Wrapper (`mvnw`)

Maven Wrapper allows a project to use Maven even if correct Maven version is not installed globally.

## Generate wrapper

```bash
mvn wrapper:wrapper
```

This creates:
- `.mvn/`
- `mvnw`
- `mvnw.cmd`

## Use wrapper

### On Windows
```bash
mvnw.cmd clean install
```

### On Linux/macOS
```bash
./mvnw clean install
```

## Beginner meaning
This helps team members use same Maven version without manual setup.

---

# 24. Docker integration with Maven

Maven projects can also be Dockerized.

That means:
- build Java application with Maven
- create JAR file
- put JAR inside Docker image
- run application inside container

---

# 25. Dockerfile for Maven project

Create this file in project root:

```dockerfile
FROM eclipse-temurin:21-jre
WORKDIR /app
ARG JAR_FILE=target/maven-automation-demo-1.0.0.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```

## Explanation line by line

### `FROM eclipse-temurin:21-jre`
Uses Java runtime image.

### `WORKDIR /app`
Sets working directory inside container.

### `ARG JAR_FILE=target/maven-automation-demo-1.0.0.jar`
Stores JAR file path.

### `COPY ${JAR_FILE} app.jar`
Copies built JAR into image.

### `ENTRYPOINT ["java", "-jar", "app.jar"]`
Runs application when container starts.

---

# 26. Build project before Docker image

First create JAR:

```bash
mvn clean package
```

Now JAR will be created inside `target`.

Then build Docker image:

```bash
docker build -t yourdockerhubusername/maven-automation-demo:1.0.0 .
```

---

# 27. dockerfile-maven-plugin

This plugin helps Maven work with Dockerfile.

## Example

```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>dockerfile-maven-plugin</artifactId>
    <configuration>
        <repository>yourdockerhubusername/maven-automation-demo</repository>
        <tag>${project.version}</tag>
        <buildArgs>
            <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
        </buildArgs>
    </configuration>
    <executions>
        <execution>
            <id>default</id>
            <goals>
                <goal>build</goal>
                <goal>tag</goal>
                <goal>push</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

## Beginner meaning
This plugin can:
- build Docker image
- tag image
- push image

directly through Maven workflow.

---

# 28. Dockerizing Maven-based applications

## Full sequence

### Step 1: Write Java code
Create application and tests.

### Step 2: Write `pom.xml`
Define project configuration.

### Step 3: Run Maven build
```bash
mvn clean package
```

### Step 4: Write Dockerfile
Create Dockerfile in project root.

### Step 5: Build Docker image
```bash
docker build -t yourdockerhubusername/maven-automation-demo:1.0.0 .
```

### Step 6: Run Docker container
```bash
docker run --rm yourdockerhubusername/maven-automation-demo:1.0.0
```

---

# 29. Pushing artifacts to registries

There are two types of pushing here:

## 29.1 Pushing Maven artifact
This means uploading JAR/WAR to remote Maven repository.

Example repositories:
- Nexus
- Artifactory

This is done using:
```bash
mvn deploy
```

## 29.2 Pushing Docker image
This means uploading container image to container registry.

Example registries:
- Docker Hub
- GitHub Container Registry (GHCR)

---

# 30. Example settings.xml for deployment

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0">
    <servers>
        <server>
            <id>company-releases</id>
            <username>your-username</username>
            <password>your-password</password>
        </server>
    </servers>
</settings>
```

## Explanation
- credentials are stored here
- `id` must match repository id in `pom.xml`

---

# 31. Useful commands summary

## Build lifecycle commands
```bash
mvn validate
mvn compile
mvn test
mvn package
mvn verify
mvn install
mvn deploy
```

## Dependency check
```bash
mvn dependency:tree
```

## Wrapper generation
```bash
mvn wrapper:wrapper
```

## Docker image build
```bash
docker build -t yourdockerhubusername/maven-automation-demo:1.0.0 .
```

## Run Docker container
```bash
docker run --rm yourdockerhubusername/maven-automation-demo:1.0.0
```

---

# 32. Full beginner workflow

Follow this order:

1. Install Java
2. Install Maven
3. Create project folder
4. Create standard Maven directory structure
5. Write `pom.xml`
6. Write Java code
7. Write test code
8. Run `mvn validate`
9. Run `mvn compile`
10. Run `mvn test`
11. Run `mvn package`
12. Check `target` folder
13. Run `mvn install`
14. Optionally configure deployment repository
15. Run `mvn deploy`
16. Create Dockerfile
17. Build Docker image
18. Run container
19. Push container image to registry if needed

---

# 33. Very short memory trick

Remember Maven like this:

- `pom.xml` = instruction file
- dependencies = libraries needed
- plugins = workers
- lifecycle = order of work
- `compile` = make bytecode
- `test` = check correctness
- `package` = create JAR
- `install` = save locally
- `deploy` = send remotely
- wrapper = same Maven version for all
- Docker integration = run app in container

---

# 34. Final conclusion

Maven is not just a tool for compiling Java code.  
It is a full automation system that:
- manages dependencies
- runs tests
- creates packages
- standardizes project structure
- supports parent-child project setup
- controls versions
- integrates with Docker
- helps publish artifacts properly

For a beginner, the best way to learn Maven is:
- create one small project
- run each lifecycle command one by one
- inspect the `target` folder
- study `pom.xml`
- practice dependency management
- then move to Docker and deployment

Once this becomes clear, Maven becomes very easy and powerful.


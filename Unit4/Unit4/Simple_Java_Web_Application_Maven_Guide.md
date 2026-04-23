# Simple Java Web Application using Maven

This guide shows how to create a **simple Java web application** using **Maven**, **Servlet**, and an **embedded Jetty server** for easy execution.

It includes:
- complete directory structure
- contents of each file
- line-by-line explanation of `pom.xml`
- Maven commands in the correct sequence
- how to run and see output in browser

---

# 1. Project overview

We will build a small web application that has:
- one **home page** (`index.html`)
- one **Java Servlet** (`HelloServlet.java`)
- one **Maven project file** (`pom.xml`)
- one **optional deployment descriptor** (`web.xml`)

When the project runs:
- visiting `/simple-java-web-app/` shows the home page
- visiting `/simple-java-web-app/hello` shows a message from the servlet

---

# 2. Final directory structure

```text
simple-java-web-app/
├── pom.xml
└── src/
    └── main/
        ├── java/
        │   └── com/
        │       └── example/
        │           └── web/
        │               └── HelloServlet.java
        └── webapp/
            ├── index.html
            └── WEB-INF/
                └── web.xml
```

---

# 3. File contents

## 3.1 `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>simple-java-web-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <name>Simple Java Web Application</name>
    <description>A basic Java web application using Servlet and Maven</description>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <failOnMissingWebXml>false</failOnMissingWebXml>
    </properties>

    <dependencies>
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>6.0.0</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>17</source>
                    <target>17</target>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.4.0</version>
                <configuration>
                    <failOnMissingWebXml>false</failOnMissingWebXml>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.eclipse.jetty.ee10</groupId>
                <artifactId>jetty-ee10-maven-plugin</artifactId>
                <version>12.0.15</version>
                <configuration>
                    <scan>1</scan>
                    <httpConnector>
                        <port>8080</port>
                    </httpConnector>
                    <webApp>
                        <contextPath>/simple-java-web-app</contextPath>
                    </webApp>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

---

## 3.2 `HelloServlet.java`

Path:

```text
src/main/java/com/example/web/HelloServlet.java
```

Code:

```java
package com.example.web;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");

        PrintWriter out = response.getWriter();
        out.println("<html>");
        out.println("<head><title>Hello Servlet</title></head>");
        out.println("<body>");
        out.println("<h1>Hello from Java Web Application</h1>");
        out.println("<p>This page is coming from HelloServlet.</p>");
        out.println("<a href='index.html'>Go back to Home Page</a>");
        out.println("</body>");
        out.println("</html>");
    }
}
```

---

## 3.3 `index.html`

Path:

```text
src/main/webapp/index.html
```

Code:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Simple Java Web App</title>
</head>
<body>
    <h1>Welcome to Simple Java Web Application</h1>
    <p>This is the home page.</p>
    <a href="hello">Click here to call HelloServlet</a>
</body>
</html>
```

---

## 3.4 `web.xml`

Path:

```text
src/main/webapp/WEB-INF/web.xml
```

Code:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
         https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">

    <display-name>Simple Java Web Application</display-name>

    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
    </welcome-file-list>

</web-app>
```

> Note: The servlet mapping is already done using `@WebServlet("/hello")`, so `web.xml` here is only used to define the welcome page.

---

# 4. Line-by-line explanation of `pom.xml`

Below is the same `pom.xml`, and after that every line is explained.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>simple-java-web-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <name>Simple Java Web Application</name>
    <description>A basic Java web application using Servlet and Maven</description>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <failOnMissingWebXml>false</failOnMissingWebXml>
    </properties>

    <dependencies>
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>6.0.0</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>17</source>
                    <target>17</target>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.4.0</version>
                <configuration>
                    <failOnMissingWebXml>false</failOnMissingWebXml>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.eclipse.jetty.ee10</groupId>
                <artifactId>jetty-ee10-maven-plugin</artifactId>
                <version>12.0.15</version>
                <configuration>
                    <scan>1</scan>
                    <httpConnector>
                        <port>8080</port>
                    </httpConnector>
                    <webApp>
                        <contextPath>/simple-java-web-app</contextPath>
                    </webApp>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

## Explanation of each line

### Project opening

- `<project ...>`
  Starts the Maven project definition.

- `xmlns="http://maven.apache.org/POM/4.0.0"`
  Declares the XML namespace for Maven POM files.

- `xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"`
  Declares XML Schema support.

- `xsi:schemaLocation="..."`
  Tells XML tools where the Maven POM schema is located.

### Model version

- `<modelVersion>4.0.0</modelVersion>`
  Defines the version of the POM model. Almost all Maven projects use `4.0.0`.

### Project identity

- `<groupId>com.example</groupId>`
  Defines the organization or package group of the project.

- `<artifactId>simple-java-web-app</artifactId>`
  Defines the project name used by Maven for output files.

- `<version>1.0-SNAPSHOT</version>`
  Defines the current project version. `SNAPSHOT` means it is under development.

- `<packaging>war</packaging>`
  Tells Maven to package the application as a **WAR** file because this is a web application.

### Human-readable project info

- `<name>Simple Java Web Application</name>`
  Gives a readable project name.

- `<description>A basic Java web application using Servlet and Maven</description>`
  Gives a short project description.

### Properties section

- `<properties>`
  Opens the section for reusable settings.

- `<maven.compiler.source>17</maven.compiler.source>`
  Tells Maven to compile source code using Java 17 syntax.

- `<maven.compiler.target>17</maven.compiler.target>`
  Tells Maven to generate Java 17 compatible bytecode.

- `<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>`
  Sets the file encoding to UTF-8.

- `<failOnMissingWebXml>false</failOnMissingWebXml>`
  Prevents build failure if `web.xml` is missing. This is useful when servlet annotations are used.

- `</properties>`
  Closes the properties section.

### Dependencies section

- `<dependencies>`
  Opens the dependencies block.

- `<dependency>`
  Starts one library definition.

- `<groupId>jakarta.servlet</groupId>`
  Group name of the Servlet API.

- `<artifactId>jakarta.servlet-api</artifactId>`
  Artifact name of the Servlet API library.

- `<version>6.0.0</version>`
  Version of the Servlet API used in the project.

- `<scope>provided</scope>`
  Means this library is needed for compilation, but the server provides it at runtime.

- `</dependency>`
  Ends the dependency definition.

- `</dependencies>`
  Closes the dependencies block.

### Build section

- `<build>`
  Opens build customization section.

- `<plugins>`
  Opens list of Maven plugins.

### Compiler plugin

- `<plugin>`
  Starts plugin definition.

- `<groupId>org.apache.maven.plugins</groupId>`
  Maven official plugin group.

- `<artifactId>maven-compiler-plugin</artifactId>`
  Plugin used to compile Java code.

- `<version>3.11.0</version>`
  Version of the compiler plugin.

- `<configuration>`
  Opens plugin configuration.

- `<source>17</source>`
  Compile source using Java 17 language rules.

- `<target>17</target>`
  Generate class files for Java 17.

- `</configuration>`
  Closes compiler configuration.

- `</plugin>`
  Ends compiler plugin block.

### WAR plugin

- `<plugin>`
  Starts next plugin.

- `<groupId>org.apache.maven.plugins</groupId>`
  Official Maven plugin group.

- `<artifactId>maven-war-plugin</artifactId>`
  Plugin used to build WAR file.

- `<version>3.4.0</version>`
  Version of WAR plugin.

- `<configuration>`
  Opens WAR plugin settings.

- `<failOnMissingWebXml>false</failOnMissingWebXml>`
  Avoids failure when `web.xml` is absent or optional.

- `</configuration>`
  Closes WAR plugin settings.

- `</plugin>`
  Ends WAR plugin block.

### Jetty plugin

- `<plugin>`
  Starts Jetty plugin block.

- `<groupId>org.eclipse.jetty.ee10</groupId>`
  Group of the Jetty plugin for EE10/Jakarta-based applications.

- `<artifactId>jetty-ee10-maven-plugin</artifactId>`
  Plugin that runs the web application directly using Jetty.

- `<version>12.0.15</version>`
  Version of the Jetty Maven plugin.

- `<configuration>`
  Opens Jetty plugin settings.

- `<scan>1</scan>`
  Tells Jetty to scan for changes every 1 second.

- `<httpConnector>`
  Opens HTTP connector settings.

- `<port>8080</port>`
  Runs the application on port 8080.

- `</httpConnector>`
  Closes HTTP connector block.

- `<webApp>`
  Opens web application settings.

- `<contextPath>/simple-java-web-app</contextPath>`
  Sets the application URL path.

- `</webApp>`
  Closes web application settings.

- `</configuration>`
  Closes Jetty configuration.

- `</plugin>`
  Ends Jetty plugin block.

### Closing tags

- `</plugins>` closes plugin list.
- `</build>` closes build section.
- `</project>` closes the complete Maven project.

---

# 5. How to create the project manually

## Step 1: Create main project folder

```bat
mkdir simple-java-web-app
cd simple-java-web-app
```

## Step 2: Create directory structure

```bat
mkdir src
mkdir src\main
mkdir src\main\java
mkdir src\main\java\com
mkdir src\main\java\com\example
mkdir src\main\java\com\example\web
mkdir src\main\webapp
mkdir src\main\webapp\WEB-INF
```

## Step 3: Create files

Create these files:
- `pom.xml`
- `src\main\java\com\example\web\HelloServlet.java`
- `src\main\webapp\index.html`
- `src\main\webapp\WEB-INF\web.xml`

Then paste the file contents from the previous sections.

---

# 6. Maven command sequence to build and show output

Run these commands from the project root folder.

## 6.1 Check Java and Maven

```bat
java -version
mvn -version
```

## 6.2 Clean old build files

```bat
mvn clean
```

### What it does
Deletes the `target` folder if it exists.

---

## 6.3 Compile the project

```bat
mvn compile
```

### What it does
- downloads required dependencies
- compiles Java source code
- creates `.class` files in `target/classes`

---

## 6.4 Package the application

```bat
mvn package
```

### What it does
- compiles the project
- processes web resources
- creates WAR file inside `target`

Expected output file:

```text
target/simple-java-web-app-1.0-SNAPSHOT.war
```

---

## 6.5 Run the web application using Jetty

```bat
mvn jetty:run
```

### What it does
- starts embedded Jetty server
- deploys your web application
- runs it at port `8080`

---

# 7. Browser URLs to check output

After running:

```bat
mvn jetty:run
```

open browser and visit:

## Home page

```text
http://localhost:8080/simple-java-web-app/
```

## Servlet page

```text
http://localhost:8080/simple-java-web-app/hello
```

---

# 8. Expected output in browser

## Home page output

You should see:

```text
Welcome to Simple Java Web Application
This is the home page.
Click here to call HelloServlet
```

## Servlet page output

You should see:

```text
Hello from Java Web Application
This page is coming from HelloServlet.
Go back to Home Page
```

---

# 9. Important Maven commands and purpose

## `mvn clean`
Deletes previous build output.

## `mvn compile`
Compiles Java source code.

## `mvn test`
Runs test cases if test classes exist.

## `mvn package`
Creates deployable WAR file.

## `mvn install`
Copies built artifact into local Maven repository.

## `mvn jetty:run`
Runs the web application using embedded Jetty.

---

# 10. Suggested full command sequence for classroom demo

```bat
cd simple-java-web-app
mvn clean
mvn compile
mvn package
mvn jetty:run
```

Then open:

```text
http://localhost:8080/simple-java-web-app/
```

---

# 11. How to stop the server

In the same Command Prompt where Jetty is running, press:

```text
Ctrl + C
```

---

# 12. Common errors and fixes

## Error: `mvn is not recognized`
### Fix
Maven is not installed or not added to PATH.

## Error: `java is not recognized`
### Fix
JDK is not installed or not added to PATH.

## Error: Port 8080 already in use
### Fix
Change port in `pom.xml` inside Jetty plugin.

Example:

```xml
<port>9090</port>
```

Then run again using:

```bat
mvn jetty:run
```

## Error: Servlet API not found
### Fix
Run:

```bat
mvn clean compile
```

so Maven downloads dependencies properly.

---

# 13. Summary

This project demonstrates a complete **simple Java web application** using:
- **Maven** for project management
- **Servlet** for server-side Java logic
- **HTML** for the front page
- **Jetty Maven Plugin** to run the web app directly

This is a good beginner-friendly web application because it teaches:
- Maven structure
- WAR packaging
- servlet programming
- browser output
- command-line build and execution

---

# 14. Optional improvement ideas

You can later extend this project by adding:
- JSP pages
- form handling
- database connectivity
- CSS styling
- session handling
- login page
- Spring Boot version of same application


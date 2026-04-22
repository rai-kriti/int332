# Maven Java Application Example

This is one complete **beginner-friendly Maven example** of a Java console application, with the **full directory structure**, **all file contents**, and the **exact sequence of steps**.

## What you will build

A simple Java app that:

- prints a message
- adds two numbers
- runs one test through Maven
- creates a runnable JAR

## Final folder structure

```text
maven-hello/
│
├── pom.xml
└── src/
    ├── main/
    │   └── java/
    │       └── com/
    │           └── example/
    │               └── App.java
    │
    └── test/
        └── java/
            └── com/
                └── example/
                    └── AppTest.java
```

---

## Step 1: Create the project folder

Open Command Prompt and run:

```cmd
mkdir maven-hello
cd maven-hello
mkdir src
mkdir src\main
mkdir src\main\java
mkdir src\main\java\com
mkdir src\main\java\com\example
mkdir src\test
mkdir src\test\java
mkdir src\test\java\com
mkdir src\test\java\com\example
```

---

## Step 2: Create `pom.xml`

Create a file named **`pom.xml`** in the root folder `maven-hello`.

### `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>maven-hello</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>maven-hello</name>

    <properties>
        <maven.compiler.release>17</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

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
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
            </plugin>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>com.example.App</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

### What this file does

- `groupId` = logical group name of your project
- `artifactId` = project name
- `version` = project version
- `dependencies` = external libraries needed by the project
- `scope test` = JUnit is used only for testing
- `maven-jar-plugin` = adds the main class so `java -jar` works

---

## Step 3: Create the main Java file

Create this file:

**`src/main/java/com/example/App.java`**

```java
package com.example;

public class App {

    public static int add(int a, int b) {
        return a + b;
    }

    public static void main(String[] args) {
        System.out.println("Hello from Maven!");
        int result = add(10, 20);
        System.out.println("10 + 20 = " + result);
    }
}
```

### What this code does

- `package com.example;` tells Java the package name
- `add()` is a simple method
- `main()` is the entry point of the program
- `System.out.println()` prints output to the terminal

---

## Step 4: Create the test file

Create this file:

**`src/test/java/com/example/AppTest.java`**

```java
package com.example;

import org.junit.Test;
import static org.junit.Assert.assertEquals;

public class AppTest {

    @Test
    public void testAdd() {
        assertEquals(30, App.add(10, 20));
    }
}
```

### What this test does

- `@Test` marks the method as a test
- `assertEquals(30, App.add(10, 20))` checks whether the method returns the correct value

---

## Step 5: Verify Java and Maven

From the project folder or any Command Prompt window, run:

```cmd
java -version
mvn -v
```

---

## Step 6: Compile the project

Run:

```cmd
mvn compile
```

### What happens

- Maven reads `pom.xml`
- downloads required dependencies/plugins if needed
- compiles Java source files from `src/main/java`
- puts compiled `.class` files in the `target` folder

---

## Step 7: Run the test

Run:

```cmd
mvn test
```

### What happens

- Maven compiles the test code
- runs the JUnit test from `src/test/java`
- reports whether the test passed or failed

---

## Step 8: Package the application

Run:

```cmd
mvn package
```

### What happens

- your code is compiled
- tests are run
- a JAR file is created inside the `target` folder

After this, you should get a file like:

```text
target\maven-hello-1.0-SNAPSHOT.jar
```

---

## Step 9: Run the generated JAR

Run:

```cmd
java -jar target\maven-hello-1.0-SNAPSHOT.jar
```

### Expected output

```text
Hello from Maven!
10 + 20 = 30
```

---

## Step 10: Clean the build files

Run:

```cmd
mvn clean
```

### What happens

- Maven deletes the `target` folder
- this removes compiled classes and generated JARs
- useful when you want a fresh rebuild

---

## Exact sequence of commands

Use these commands in this order:

```cmd
mkdir maven-hello
cd maven-hello
mkdir src
mkdir src\main
mkdir src\main\java
mkdir src\main\java\com
mkdir src\main\java\com\example
mkdir src\test
mkdir src\test\java
mkdir src\test\java\com
mkdir src\test\java\com\example
```

Then create these files manually:

- `pom.xml`
- `src/main/java/com/example/App.java`
- `src/test/java/com/example/AppTest.java`

Then run:

```cmd
java -version
mvn -v
mvn compile
mvn test
mvn package
java -jar target\maven-hello-1.0-SNAPSHOT.jar
mvn clean
```

---

## What each important folder means

- `src/main/java` → main application code
- `src/test/java` → test code
- `target` → generated output after build
- `pom.xml` → Maven configuration file for project, dependencies, plugins, and build settings

---

## Very important beginner note

When you run Maven for the first time:

- it may take some time
- Maven may download dependencies and plugins from repositories
- this is normal

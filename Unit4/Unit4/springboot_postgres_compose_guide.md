# Deploying Java Spring Boot + PostgreSQL with Docker Compose

## Question 7: Deploying Java Spring Boot + PostgreSQL

### Real-Life Situation
A fintech startup runs a Spring Boot backend service that connects to a PostgreSQL database.

### Task
Deploy Spring Boot and PostgreSQL containers using Docker Compose.

---

# 1. Goal
In this solution, you will create a complete Dockerized Spring Boot + PostgreSQL project that:

- runs PostgreSQL in one container
- runs Spring Boot in another container
- connects Spring Boot to PostgreSQL using Docker Compose networking
- stores database data in a persistent Docker volume
- exposes the Spring Boot API on port `8080`
- exposes PostgreSQL on port `5432`

This guide includes:

- full directory structure
- content of all important files
- exact commands in sequence
- build and run steps
- verification steps
- troubleshooting tips

---

# 2. Final Project Directory Structure

```text
fintech-springboot-postgres/
│
├── docker-compose.yml
├── Dockerfile
├── .dockerignore
├── pom.xml
│
└── src/
    └── main/
        ├── java/
        │   └── com/
        │       └── example/
        │           └── fintech/
        │               ├── FintechApplication.java
        │               │
        │               ├── controller/
        │               │   └── AccountController.java
        │               │
        │               ├── entity/
        │               │   └── Account.java
        │               │
        │               ├── repository/
        │               │   └── AccountRepository.java
        │               │
        │               └── service/
        │                   └── AccountService.java
        │
        └── resources/
            └── application.properties
```

---

# 3. What This Application Does

This sample fintech backend manages accounts.

It supports these API operations:

- `GET /accounts` → fetch all accounts
- `POST /accounts` → add a new account
- `GET /accounts/{id}` → fetch one account by ID
- `DELETE /accounts/{id}` → delete an account

Sample JSON for POST:

```json
{
  "customerName": "Alok",
  "accountType": "Savings",
  "balance": 15000.75
}
```

---

# 4. Complete File Contents

## 4.1 `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.3.5</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>fintech</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>fintech</name>
    <description>Spring Boot with PostgreSQL using Docker Compose</description>

    <properties>
        <java.version>21</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

### Purpose
- adds Spring Web for REST APIs
- adds Spring Data JPA for database operations
- adds PostgreSQL driver
- configures Spring Boot build process

---

## 4.2 `Dockerfile`

```dockerfile
FROM maven:3.9.9-eclipse-temurin-21 AS build
WORKDIR /app

COPY pom.xml .
RUN mvn dependency:go-offline

COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:21-jdk
WORKDIR /app

COPY --from=build /app/target/fintech-0.0.1-SNAPSHOT.jar app.jar

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Purpose
This is a **multi-stage Docker build**:

- first stage builds the JAR using Maven
- second stage runs only the JAR in a lightweight Java runtime image

This keeps the final image smaller and cleaner.

---

## 4.3 `docker-compose.yml`

```yaml
services:
  postgres:
    image: postgres:16
    container_name: fintech_postgres
    environment:
      POSTGRES_DB: fintechdb
      POSTGRES_USER: fintechuser
      POSTGRES_PASSWORD: fintechpass
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U fintechuser -d fintechdb"]
      interval: 10s
      timeout: 5s
      retries: 5

  springboot:
    build: .
    container_name: fintech_springboot
    depends_on:
      postgres:
        condition: service_healthy
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/fintechdb
      SPRING_DATASOURCE_USERNAME: fintechuser
      SPRING_DATASOURCE_PASSWORD: fintechpass
      SPRING_JPA_HIBERNATE_DDL_AUTO: update
      SPRING_JPA_PROPERTIES_HIBERNATE_DIALECT: org.hibernate.dialect.PostgreSQLDialect
    ports:
      - "8080:8080"

volumes:
  postgres_data:
```

### Purpose
- starts PostgreSQL service
- starts Spring Boot service
- ensures Spring Boot waits until PostgreSQL is healthy
- creates persistent volume `postgres_data`
- enables communication using service name `postgres`

### Important Concept
Inside Docker Compose, the Spring Boot container connects to PostgreSQL using:

```text
jdbc:postgresql://postgres:5432/fintechdb
```

Here:
- `postgres` is the service name from Compose
- Docker automatically provides internal DNS resolution

---

## 4.4 `.dockerignore`

```dockerignore
target
.idea
.vscode
*.iml
.git
.gitignore
README.md
```

### Purpose
Prevents unnecessary files from being copied into Docker build context.

This makes the build faster and cleaner.

---

## 4.5 `src/main/resources/application.properties`

```properties
spring.application.name=fintech

spring.datasource.url=${SPRING_DATASOURCE_URL:jdbc:postgresql://localhost:5432/fintechdb}
spring.datasource.username=${SPRING_DATASOURCE_USERNAME:fintechuser}
spring.datasource.password=${SPRING_DATASOURCE_PASSWORD:fintechpass}

spring.jpa.hibernate.ddl-auto=${SPRING_JPA_HIBERNATE_DDL_AUTO:update}
spring.jpa.properties.hibernate.dialect=${SPRING_JPA_PROPERTIES_HIBERNATE_DIALECT:org.hibernate.dialect.PostgreSQLDialect}
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

server.port=8080
```

### Purpose
This file makes the project flexible.

- inside Docker, values come from environment variables
- outside Docker, fallback values are used

So the same project can run both locally and in containers.

---

## 4.6 `src/main/java/com/example/fintech/FintechApplication.java`

```java
package com.example.fintech;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class FintechApplication {
    public static void main(String[] args) {
        SpringApplication.run(FintechApplication.class, args);
    }
}
```

### Purpose
This is the main Spring Boot entry point.

---

## 4.7 `src/main/java/com/example/fintech/entity/Account.java`

```java
package com.example.fintech.entity;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

@Entity
@Table(name = "accounts")
public class Account {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String customerName;
    private String accountType;
    private Double balance;

    public Account() {
    }

    public Account(String customerName, String accountType, Double balance) {
        this.customerName = customerName;
        this.accountType = accountType;
        this.balance = balance;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getCustomerName() {
        return customerName;
    }

    public void setCustomerName(String customerName) {
        this.customerName = customerName;
    }

    public String getAccountType() {
        return accountType;
    }

    public void setAccountType(String accountType) {
        this.accountType = accountType;
    }

    public Double getBalance() {
        return balance;
    }

    public void setBalance(Double balance) {
        this.balance = balance;
    }
}
```

### Purpose
This class represents a database table.

Each object becomes one row in the `accounts` table.

---

## 4.8 `src/main/java/com/example/fintech/repository/AccountRepository.java`

```java
package com.example.fintech.repository;

import com.example.fintech.entity.Account;
import org.springframework.data.jpa.repository.JpaRepository;

public interface AccountRepository extends JpaRepository<Account, Long> {
}
```

### Purpose
This interface gives built-in database operations such as:

- save
- findAll
- findById
- deleteById

without writing SQL manually.

---

## 4.9 `src/main/java/com/example/fintech/service/AccountService.java`

```java
package com.example.fintech.service;

import com.example.fintech.entity.Account;
import com.example.fintech.repository.AccountRepository;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class AccountService {

    private final AccountRepository accountRepository;

    public AccountService(AccountRepository accountRepository) {
        this.accountRepository = accountRepository;
    }

    public List<Account> getAllAccounts() {
        return accountRepository.findAll();
    }

    public Optional<Account> getAccountById(Long id) {
        return accountRepository.findById(id);
    }

    public Account createAccount(Account account) {
        return accountRepository.save(account);
    }

    public void deleteAccount(Long id) {
        accountRepository.deleteById(id);
    }
}
```

### Purpose
This is the business logic layer.

It acts as a bridge between controller and repository.

---

## 4.10 `src/main/java/com/example/fintech/controller/AccountController.java`

```java
package com.example.fintech.controller;

import com.example.fintech.entity.Account;
import com.example.fintech.service.AccountService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/accounts")
public class AccountController {

    private final AccountService accountService;

    public AccountController(AccountService accountService) {
        this.accountService = accountService;
    }

    @GetMapping
    public List<Account> getAllAccounts() {
        return accountService.getAllAccounts();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Account> getAccountById(@PathVariable Long id) {
        Optional<Account> account = accountService.getAccountById(id);
        return account.map(ResponseEntity::ok)
                .orElseGet(() -> ResponseEntity.notFound().build());
    }

    @PostMapping
    public Account createAccount(@RequestBody Account account) {
        return accountService.createAccount(account);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<String> deleteAccount(@PathVariable Long id) {
        accountService.deleteAccount(id);
        return ResponseEntity.ok("Account deleted successfully");
    }
}
```

### Purpose
This class exposes REST endpoints.

---

# 5. Exact Sequence of Commands

Below is the exact command flow.

## 5.1 Create Project Folder

### Windows PowerShell

```powershell
mkdir fintech-springboot-postgres
cd fintech-springboot-postgres
```

### Create Source Folders

```powershell
mkdir src
mkdir src\main
mkdir src\main\java
mkdir src\main\resources
mkdir src\main\java\com
mkdir src\main\java\com\example
mkdir src\main\java\com\example\fintech
mkdir src\main\java\com\example\fintech\controller
mkdir src\main\java\com\example\fintech\entity
mkdir src\main\java\com\example\fintech\repository
mkdir src\main\java\com\example\fintech\service
```

Now create the files exactly as shown in Section 4.

---

## 5.2 Verify Docker Installation

```powershell
docker --version
docker compose version
```

Expected result:
- Docker should be installed
- Docker Compose should be available

---

## 5.3 Build and Start Containers

Run from the project root where `docker-compose.yml` exists.

```powershell
docker compose up --build
```

### What happens here
- PostgreSQL image is pulled
- Spring Boot image is built from Dockerfile
- PostgreSQL container starts first
- Spring Boot waits until PostgreSQL becomes healthy
- Spring Boot then starts and connects to the DB

---

## 5.4 Run in Detached Mode

If you want terminal control back, use:

```powershell
docker compose up --build -d
```

---

## 5.5 Check Running Containers

```powershell
docker ps
```

Expected containers:
- `fintech_postgres`
- `fintech_springboot`

---

## 5.6 View Logs

### Spring Boot logs

```powershell
docker logs fintech_springboot
```

### PostgreSQL logs

```powershell
docker logs fintech_postgres
```

### Follow live logs

```powershell
docker compose logs -f
```

---

# 6. Verify the Application

## 6.1 Test in Browser

Open:

```text
http://localhost:8080/accounts
```

Expected initial output:

```json
[]
```

That means:
- Spring Boot is running
- API is reachable
- database connection is working

---

## 6.2 Insert Data Using PowerShell

```powershell
Invoke-RestMethod -Uri http://localhost:8080/accounts `
  -Method POST `
  -ContentType "application/json" `
  -Body '{"customerName":"Alok","accountType":"Savings","balance":15000.75}'
```

Expected response:

```json
{
  "id": 1,
  "customerName": "Alok",
  "accountType": "Savings",
  "balance": 15000.75
}
```

---

## 6.3 Fetch All Accounts

```powershell
Invoke-RestMethod -Uri http://localhost:8080/accounts -Method GET
```

Expected response:

```json
[
  {
    "id": 1,
    "customerName": "Alok",
    "accountType": "Savings",
    "balance": 15000.75
  }
]
```

---

## 6.4 Fetch One Account By ID

```powershell
Invoke-RestMethod -Uri http://localhost:8080/accounts/1 -Method GET
```

---

## 6.5 Delete Account

```powershell
Invoke-RestMethod -Uri http://localhost:8080/accounts/1 -Method DELETE
```

---

# 7. Verify PostgreSQL Data Persistence

Add one account, then stop containers:

```powershell
docker compose down
```

Now restart:

```powershell
docker compose up -d
```

Fetch accounts again:

```powershell
Invoke-RestMethod -Uri http://localhost:8080/accounts -Method GET
```

If data still exists, persistence is working.

### Why?
Because Docker Compose uses this named volume:

```yaml
volumes:
  postgres_data:
```

This stores PostgreSQL data outside the container filesystem.

---

# 8. Stop and Remove Containers

## Stop containers

```powershell
docker compose stop
```

## Remove containers but keep volume data

```powershell
docker compose down
```

## Remove containers and volume data completely

```powershell
docker compose down -v
```

Use `-v` only when you want a fresh database.

---

# 9. How Container Communication Works

Docker Compose automatically creates a private network.

So:

- Spring Boot container can reach PostgreSQL using service name `postgres`
- PostgreSQL does not need `localhost` inside containers

### Correct container-to-container URL

```text
jdbc:postgresql://postgres:5432/fintechdb
```

### Wrong URL inside container

```text
jdbc:postgresql://localhost:5432/fintechdb
```

Why is `localhost` wrong?
Because inside a container, `localhost` refers to the same container itself, not another container.

---

# 10. Exact Working Flow

1. Docker Compose starts PostgreSQL.
2. PostgreSQL initializes database `fintechdb`.
3. Health check verifies PostgreSQL is ready.
4. Docker Compose builds Spring Boot image.
5. Spring Boot container starts.
6. Spring Boot reads DB settings from environment variables.
7. Spring Data JPA connects to PostgreSQL.
8. Hibernate creates or updates the `accounts` table.
9. API becomes available on port `8080`.

---

# 11. Common Errors and Fixes

## Error 1: `Error: Invalid or corrupt jarfile app.jar`

### Cause
The JAR was not built correctly or the wrong file was copied.

### Fix
Make sure Dockerfile contains:

```dockerfile
COPY --from=build /app/target/fintech-0.0.1-SNAPSHOT.jar app.jar
```

Also ensure `artifactId` and `version` in `pom.xml` match the JAR name.

Then rebuild:

```powershell
docker compose down
docker compose up --build
```

---

## Error 2: Spring Boot exits because database is not ready

### Cause
Spring Boot starts before PostgreSQL finishes initialization.

### Fix
Use healthcheck and `depends_on` exactly as shown in `docker-compose.yml`.

---

## Error 3: `Connection refused`

### Cause
Usually one of these:
- PostgreSQL container not running
- wrong JDBC URL
- wrong username/password
- wrong port

### Fix
Check:

```powershell
docker ps
docker logs fintech_postgres
docker logs fintech_springboot
```

Verify JDBC URL is:

```text
jdbc:postgresql://postgres:5432/fintechdb
```

---

## Error 4: Port already in use

### Cause
Port `8080` or `5432` is already occupied.

### Fix
Change port mapping in `docker-compose.yml`.

Example:

```yaml
ports:
  - "8081:8080"
```

Then access:

```text
http://localhost:8081/accounts
```

---

## Error 5: Table not created

### Cause
JPA configuration issue.

### Fix
Ensure:

```properties
spring.jpa.hibernate.ddl-auto=update
```

and PostgreSQL dialect is set correctly.

---

# 12. Useful Docker Commands for This Project

## Build again

```powershell
docker compose build
```

## Start existing containers

```powershell
docker compose up -d
```

## Restart services

```powershell
docker compose restart
```

## Remove everything including volumes

```powershell
docker compose down -v
```

## Show Compose services

```powershell
docker compose ps
```

## Open shell in Spring Boot container

```powershell
docker exec -it fintech_springboot sh
```

## Open PostgreSQL shell

```powershell
docker exec -it fintech_postgres psql -U fintechuser -d fintechdb
```

## Check tables inside PostgreSQL

Once inside `psql`, run:

```sql
\dt
select * from accounts;
```

---

# 13. Interview or Viva Explanation

If asked in viva, explain the project like this:

> We used Docker Compose to orchestrate two containers: one for Spring Boot and one for PostgreSQL. The Spring Boot app was built using a multi-stage Dockerfile. Docker Compose created a shared network automatically, so the app connected to the database using the service name `postgres`. A named volume was attached to PostgreSQL to make data persistent. Health checks ensured the application started only after the database was ready.

---

# 14. Short Summary

This solution demonstrates a production-style beginner-friendly deployment of a Spring Boot backend with PostgreSQL using Docker Compose.

It covers:

- multi-container deployment
- Docker networking
- environment-based configuration
- persistent storage with volumes
- health checks
- REST API verification

---

# 15. One-Page Command Summary

```powershell
mkdir fintech-springboot-postgres
cd fintech-springboot-postgres

mkdir src
mkdir src\main
mkdir src\main\java
mkdir src\main\resources
mkdir src\main\java\com
mkdir src\main\java\com\example
mkdir src\main\java\com\example\fintech
mkdir src\main\java\com\example\fintech\controller
mkdir src\main\java\com\example\fintech\entity
mkdir src\main\java\com\example\fintech\repository
mkdir src\main\java\com\example\fintech\service

# create files with the exact contents given above

docker --version
docker compose version

docker compose up --build -d
docker ps
docker compose logs -f

Invoke-RestMethod -Uri http://localhost:8080/accounts -Method GET

Invoke-RestMethod -Uri http://localhost:8080/accounts `
  -Method POST `
  -ContentType "application/json" `
  -Body '{"customerName":"Alok","accountType":"Savings","balance":15000.75}'

Invoke-RestMethod -Uri http://localhost:8080/accounts -Method GET
Invoke-RestMethod -Uri http://localhost:8080/accounts/1 -Method GET
Invoke-RestMethod -Uri http://localhost:8080/accounts/1 -Method DELETE

docker compose down
docker compose up -d
docker compose down -v
```

---

# 16. Optional Improvement Ideas

You can extend this project later with:

- Swagger/OpenAPI documentation
- validation using `@Valid`
- global exception handling
- custom SQL scripts
- pgAdmin container
- actuator health endpoints
- unit and integration tests
- separate `dev` and `prod` profiles

---

# 17. Final Note

If you copy the files exactly as given and run the commands from the project root, the project should start successfully using Docker Compose.

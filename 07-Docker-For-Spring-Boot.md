# Docker For Spring Boot

## Why I Am Writing This

After understanding:

* Dockerfiles
* Images
* Containers
* Compose
* Networks
* Volumes

I still got confused when deploying TeamVault.

Questions I had:

* Why do Spring projects need Docker?
* Why create a JAR?
* Why do we need a Dockerfile?
* What is a multi-stage build?
* How do environment variables reach Spring?
* How do Spring Profiles work in Docker?
* How does Render run my Spring Boot app?

This document answers those questions.

---

# The Big Picture

A Spring Boot application starts as:

```text
Java Source Code
```

Then:

```text
Java Source Code
        ↓
Maven Build
        ↓
JAR File
        ↓
Docker Image
        ↓
Docker Container
        ↓
Running Application
```

Understanding this flow is the key.

---

# Step 1 - Source Code

Initially TeamVault exists as:

```text
src/main/java

src/main/resources

pom.xml
```

This is source code.

Servers cannot directly run source code.

---

# Step 2 - Maven Build

Command:

```bash
mvn clean package
```

Maven compiles:

```text
Java Source Code
```

into:

```text
Bytecode
```

and packages everything into:

```text
teamvault.jar
```

---

# What Is A JAR?

JAR means:

```text
Java Archive
```

Think:

```text
ZIP File
```

for Java applications.

Contains:

```text
Compiled Classes

Dependencies

Resources

Configuration
```

---

# Why Not Deploy Source Code?

Imagine Render receives:

```text
src/

pom.xml
```

It must:

```text
Install Java

Install Maven

Compile Code

Build JAR
```

every time.

Instead:

```text
Build Once
Deploy JAR
```

is simpler.

---

# Running A JAR

Locally:

```bash
java -jar teamvault.jar
```

Spring Boot starts.

That's it.

---

# Docker's Role

Docker doesn't care about Spring.

Docker only knows:

```text
Run Processes
```

Our goal becomes:

```text
Run:

java -jar teamvault.jar
```

inside a container.

---

# Simplest Spring Dockerfile

```dockerfile
FROM eclipse-temurin:21-jre

COPY target/teamvault.jar app.jar

ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

# Understanding It

FROM

```dockerfile
FROM eclipse-temurin:21-jre
```

Means:

```text
Use image that already contains Java 21.
```

No need to install Java manually.

---

COPY

```dockerfile
COPY target/teamvault.jar app.jar
```

Means:

```text
Take generated JAR

Put it inside image
```

---

ENTRYPOINT

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Means:

```text
When container starts

Run Spring Boot
```

---

# Build Flow

Build JAR:

```bash
mvn clean package
```

Build Docker Image:

```bash
docker build -t teamvault .
```

Run Container:

```bash
docker run teamvault
```

Application starts.

---

# Problem With Simple Dockerfile

This works.

But it assumes:

```text
JAR already exists.
```

Meaning:

```text
Developer must run Maven first.
```

Not ideal.

---

# Multi-Stage Builds

Professional Dockerfiles usually use:

```text
Multi-Stage Build
```

---

# Why Multi-Stage Builds Exist

We need:

```text
Maven
```

for building.

But we don't need:

```text
Maven
```

for running.

Keeping Maven inside final image:

```text
Larger Image

Slower Deployments
```

---

# Multi-Stage Idea

Stage 1:

```text
Build Application
```

Stage 2:

```text
Run Application
```

---

# Example

```dockerfile
# Build Stage
FROM maven:3.9-eclipse-temurin-21 AS builder

WORKDIR /app

COPY . .

RUN mvn clean package -DskipTests

# Runtime Stage
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=builder /app/target/*.jar app.jar

ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

# What Happens?

Stage 1:

```text
Install Maven

Compile Code

Generate JAR
```

---

Stage 2:

```text
Copy JAR only
```

Final image contains:

```text
Java Runtime

JAR
```

No Maven.

Smaller image.

---

# Why Companies Prefer This

Smaller image means:

```text
Faster Upload

Faster Download

Lower Storage

Lower Costs
```

---

# Spring Environment Variables

Remember:

Bad:

```properties
spring.datasource.password=password123
```

---

Good:

```properties
spring.datasource.password=${DB_PASSWORD}
```

Spring now expects:

```text
DB_PASSWORD
```

at runtime.

---

# Docker Environment Variables

Container can receive:

```bash
docker run \
-e DB_PASSWORD=secret \
teamvault
```

Spring sees:

```text
DB_PASSWORD
```

and injects value.

---

# How Spring Resolves Variables

Example:

```properties
spring.datasource.password=${DB_PASSWORD}
```

Container:

```text
DB_PASSWORD=mysecret
```

Spring replaces:

```text
${DB_PASSWORD}
```

with:

```text
mysecret
```

during startup.

---

# Spring Profiles

This confused me a lot.

---

# Problem

Local machine:

```text
Local PostgreSQL
```

Production:

```text
Cloud PostgreSQL
```

Need different configs.

---

# Profile Solution

Spring supports:

```text
Development

Testing

Production
```

profiles.

---

# Example Files

```text
application.properties

application-dev.properties

application-prod.properties
```

---

# Development

application-dev.properties

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/teamvault
```

---

# Production

application-prod.properties

```properties
spring.datasource.url=${DB_URL}
```

---

# Activating Profile

Environment Variable:

```text
SPRING_PROFILES_ACTIVE=prod
```

Spring loads:

```text
application.properties

+

application-prod.properties
```

---

# Property Override Rule

If both contain:

```properties
spring.datasource.url
```

last loaded profile wins.

No conflicts.

No crashes.

---

# How Render Uses Environment Variables

Render Dashboard:

```text
DB_URL=...

DB_USERNAME=...

DB_PASSWORD=...

JWT_SECRET=...
```

---

Container Starts

↓

Spring Reads:

```properties
${DB_URL}
```

↓

Render Provides Value

↓

Application Connects To Database

---

# Render Deployment Flow

Source Code:

```text
GitHub
```

↓

Dockerfile

↓

Docker Image

↓

Render

↓

Container

↓

Spring Boot Starts

↓

Connects To PostgreSQL

---

# Why Docker Is Useful For Spring

Without Docker:

```text
Install Java

Install Maven

Configure Environment

Run JAR
```

Manually.

---

With Docker:

```text
docker run teamvault
```

Everything already packaged.

---

# TeamVault Example

Development:

```text
Laptop

↓

Maven

↓

JAR

↓

Docker Image
```

Deployment:

```text
Render

↓

Container

↓

Spring Boot
```

Configuration:

```text
Render Environment Variables

↓

Spring Properties

↓

Database Connection
```

---

# Mental Model

If I only remember one thing:

```text
Spring Boot Project
        ↓
Maven Build
        ↓
JAR File
        ↓
Docker Image
        ↓
Docker Container
        ↓
Running Application
```

Docker's job is NOT to run Spring.

Docker's job is:

```text
Package Spring

Package Java

Package Dependencies

Run Everything Consistently
```

on any machine.

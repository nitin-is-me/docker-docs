# Images, Containers and Dockerfiles

## Why I Am Writing This

Most Docker confusion comes from mixing up:

* Dockerfile
* Image
* Container

When I started learning Docker, these three words felt interchangeable.

They are not.

Understanding their relationship is the foundation of Docker.

---

# The Most Important Docker Flow

Everything in Docker follows this pipeline:

```text
Dockerfile
    ↓
docker build
    ↓
Image
    ↓
docker run
    ↓
Container
```

If I remember nothing else from this document, remember this.

---

# Real World Analogy

Think about cooking.

Recipe:

```text
Instructions for making a cake
```

Cake:

```text
Finished cake
```

Person eating cake:

```text
Using the cake
```

Docker equivalent:

```text
Dockerfile
    ↓
Image
    ↓
Container
```

---

# What Is A Dockerfile?

A Dockerfile is a text file.

It contains instructions for building an image.

Example:

```dockerfile
FROM eclipse-temurin:21-jre

COPY target/teamvault.jar app.jar

ENTRYPOINT ["java", "-jar", "app.jar"]
```

Docker reads these instructions and creates an image.

---

# Think Of Dockerfile As Source Code

Java:

```text
Source Code
    ↓
Compile
    ↓
Bytecode
```

Docker:

```text
Dockerfile
    ↓
Build
    ↓
Image
```

Dockerfile itself does not run.

It only describes how to create something that can run.

---

# Understanding Each Instruction

Example:

```dockerfile
FROM eclipse-temurin:21-jre
```

Meaning:

```text
Start with an image
that already contains Java 21.
```

Think:

```text
I don't want to install Java myself.

Use an image where Java is already installed.
```

---

# Base Images

Every image starts from another image.

Example:

```dockerfile
FROM ubuntu
```

or

```dockerfile
FROM postgres:17
```

or

```dockerfile
FROM eclipse-temurin:21-jre
```

This is called the:

```text
Base Image
```

---

# COPY Instruction

Example:

```dockerfile
COPY target/teamvault.jar app.jar
```

Meaning:

```text
Take teamvault.jar
from my computer

and place it

inside image as app.jar
```

---

# ENTRYPOINT

Example:

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Meaning:

```text
When container starts,
run this command.
```

Equivalent:

```bash
java -jar app.jar
```

---

# What Is An Image?

An image is a packaged application.

Think:

```text
Application
+
Dependencies
+
Runtime
+
Configuration
```

all bundled together.

---

# Image Characteristics

Image is:

```text
Read Only

Stored

Not Running
```

Think:

```text
Blueprint
```

or

```text
Snapshot
```

---

# Example

Suppose I build TeamVault.

Docker creates:

```text
teamvault:latest
```

This image contains:

```text
Java Runtime
Spring Boot Jar
Dependencies
```

It does not run yet.

It only exists as a package.

---

# Viewing Images

Command:

```bash
docker images
```

Example Output:

```text
REPOSITORY     TAG
teamvault      latest
postgres       17
```

These are images.

Not containers.

---

# What Is A Container?

A container is:

```text
A running instance
of an image.
```

---

# Java Analogy

Java:

```java
class User
```

↓

```java
new User()
```

Docker:

```text
Image
```

↓

```text
Container
```

---

# Important Rule

One image can create many containers.

Example:

```text
Image:
TeamVault
```

Container 1:

```text
Running TeamVault
```

Container 2:

```text
Running TeamVault
```

Container 3:

```text
Running TeamVault
```

Same image.

Multiple running containers.

---

# Why This Matters

Suppose TeamVault grows.

One server is overloaded.

Docker can start:

```text
Container 1
Container 2
Container 3
Container 4
```

all from same image.

This is one reason Docker is popular for scaling.

---

# Creating An Image

Command:

```bash
docker build -t teamvault .
```

Let's break it down.

---

# docker build

Means:

```text
Read Dockerfile
and create image.
```

---

# -t

Means:

```text
Tag
or
Name
```

Example:

```bash
docker build -t teamvault .
```

creates:

```text
teamvault
```

image.

---

# Dot (.)

Means:

```text
Current Directory
```

Docker looks for:

```text
Dockerfile
```

in current folder.

---

# Running A Container

Command:

```bash
docker run teamvault
```

Meaning:

```text
Create container
from teamvault image.
```

---

# Lifecycle Visualization

Build:

```text
Dockerfile
    ↓
Image
```

Run:

```text
Image
    ↓
Container
```

Stop:

```text
Container stopped
```

Delete:

```text
Container removed
```

Image still exists.

---

# Why Image Survives

Think:

```text
Recipe survives

even if cake is eaten.
```

Similarly:

```text
Image survives

even if container stops.
```

You can always create a new container.

---

# Container States

Containers can be:

```text
Running
Stopped
Removed
```

---

# View Running Containers

Command:

```bash
docker ps
```

Shows:

```text
Only running containers.
```

---

# View All Containers

Command:

```bash
docker ps -a
```

Shows:

```text
Running
Stopped
```

containers.

---

# Stop Container

Command:

```bash
docker stop container_id
```

Container stops.

Image remains.

---

# Remove Container

Command:

```bash
docker rm container_id
```

Container deleted.

Image remains.

---

# Remove Image

Command:

```bash
docker rmi image_name
```

Image deleted.

---

# Port Mapping

This confuses almost everyone.

Suppose Spring Boot runs:

```text
8080
```

inside container.

Container is isolated.

My browser cannot automatically access it.

Need port mapping.

---

# Example

Command:

```bash
docker run -p 8080:8080 teamvault
```

Meaning:

```text
Host Machine Port 8080
        ↓
Container Port 8080
```

Now browser can access:

```text
http://localhost:8080
```

---

# Another Example

```bash
docker run -p 9000:8080 teamvault
```

Meaning:

```text
My Laptop:9000
        ↓
Container:8080
```

Browser:

```text
http://localhost:9000
```

works.

---

# Environment Variables

Instead of:

```properties
spring.datasource.password=password123
```

we use:

```properties
spring.datasource.password=${DB_PASSWORD}
```

Run container:

```bash
docker run \
-e DB_PASSWORD=secret \
teamvault
```

Container receives:

```text
DB_PASSWORD=secret
```

at runtime.

---

# TeamVault Example

Development:

```text
Spring Boot Project
```

↓

Build Jar:

```text
teamvault.jar
```

↓

Docker Build:

```text
teamvault image
```

↓

Docker Run:

```text
teamvault container
```

↓

Application Live

---

# Mental Model

If I only remember one thing:

```text
Dockerfile
=
Recipe

Image
=
Packaged Application

Container
=
Running Application
```

Flow:

```text
Dockerfile
    ↓
Build
    ↓
Image
    ↓
Run
    ↓
Container
```

Everything else in Docker builds on this relationship.

# Complete Deployment Flow - From PC To Production

## Why I Am Writing This

After learning:

* Docker
* Dockerfiles
* Images
* Containers
* Docker Hub
* GitHub Actions
* Spring Profiles
* Environment Variables
* Render

I understood each topic individually.

But I still couldn't answer:

> What actually happens when I push code?

This document connects everything together.

The goal is to understand the complete deployment pipeline from my PC to a live application.

---

# The Big Picture

The entire journey looks like this:

```text
My PC
    ↓
GitHub
    ↓
GitHub Actions
    ↓
Docker Build
    ↓
Docker Hub
    ↓
Render
    ↓
Docker Container
    ↓
Spring Boot
    ↓
PostgreSQL
```

Every deployment follows this flow.

---

# Step 1 - Writing Code

Initially TeamVault exists only on my PC.

Example:

```text
src/main/java

src/main/resources

pom.xml

Dockerfile
```

At this stage:

```text
Nobody else can access it.
```

Everything exists locally.

---

# Step 2 - Git Commit

Example:

```bash
git add .

git commit -m "Added article search"
```

This creates a snapshot.

Still only exists locally.

---

# Step 3 - Push To GitHub

```bash
git push origin main
```

Now GitHub receives:

```text
Source Code

README

Dockerfile

pom.xml
```

Important:

GitHub does NOT receive:

```text
Docker Image

Running Container

Database
```

Only source code.

---

# What GitHub Stores

GitHub stores:

```text
Source Code
```

not:

```text
Built Application
```

This distinction is extremely important.

---

# Step 4 - GitHub Actions Trigger

GitHub detects:

```text
New Push
```

and automatically starts:

```text
Workflow
```

Example:

```yaml
on:
  push:
    branches:
      - main
```

Meaning:

```text
Push To Main
↓
Start Workflow
```

---

# Step 5 - GitHub Creates Runner

GitHub creates:

```text
Temporary Virtual Machine
```

called a:

```text
Runner
```

Think:

```text
Temporary Computer
```

owned by GitHub.

---

# Runner Lifecycle

```text
Created
↓
Downloads Code
↓
Runs Workflow
↓
Destroyed
```

Every push gets a fresh machine.

---

# Step 6 - Checkout Source Code

GitHub downloads repository contents.

Example:

```yaml
uses: actions/checkout@v4
```

Now runner contains:

```text
TeamVault Source Code
```

---

# Step 7 - Build Docker Image

GitHub executes:

```bash
docker build .
```

Docker reads:

```text
Dockerfile
```

and starts building image.

---

# Docker Build Process

Dockerfile:

```dockerfile
FROM eclipse-temurin:21-jre

COPY target/teamvault.jar app.jar

ENTRYPOINT ["java", "-jar", "app.jar"]
```

Docker creates:

```text
Docker Image
```

containing:

```text
Java Runtime

Spring Boot JAR

Dependencies
```

---

# Important

At this point:

```text
Image Exists
```

but only inside:

```text
GitHub Runner
```

Render still cannot access it.

---

# Step 8 - Login To Docker Hub

GitHub uses stored secrets.

Example:

```text
DOCKER_USERNAME

DOCKER_PASSWORD
```

Workflow logs in automatically.

Equivalent to:

```bash
docker login
```

on my laptop.

---

# Step 9 - Push Image To Docker Hub

GitHub executes:

```bash
docker push
```

Image uploaded.

Now:

```text
Docker Hub
```

contains:

```text
teamvault:v1.0
```

---

# Why Docker Hub Is Needed

Without Docker Hub:

```text
Image Exists Only On Runner
```

When runner is destroyed:

```text
Image Disappears
```

Docker Hub provides permanent storage.

---

# Step 10 - Render Notices New Image

Render watches:

```text
Docker Hub
```

or receives deployment trigger.

Render downloads:

```text
teamvault:v1.0
```

---

# Render Pull Process

```text
Docker Hub
↓
Render Downloads Image
↓
Image Available On Render
```

---

# Step 11 - Render Creates Container

Render executes:

```bash
docker run
```

behind the scenes.

Container starts.

---

# Important Distinction

Docker Hub stores:

```text
Image
```

Render runs:

```text
Container
```

Never confuse these.

---

# Step 12 - Environment Variables

Container starts.

Spring sees:

```properties
spring.datasource.url=${DB_URL}
```

Question:

```text
Where is DB_URL?
```

Answer:

Render.

---

# Render Dashboard

Environment Variables:

```text
DB_URL=...

DB_USERNAME=...

DB_PASSWORD=...

JWT_SECRET=...
```

Render injects these into container.

---

# Spring Resolution

Spring sees:

```properties
${DB_URL}
```

and replaces it with:

```text
Actual Database URL
```

during startup.

---

# Step 13 - Spring Boot Starts

Docker executes:

```bash
java -jar app.jar
```

inside container.

Spring Boot begins startup.

---

# Spring Initialization

Spring:

```text
Loads Beans

Loads Configuration

Loads Security

Loads Repositories

Connects Database
```

---

# Step 14 - Database Connection

Spring reads:

```properties
spring.datasource.url
```

and attempts connection.

Example:

```text
Supabase PostgreSQL

or

Render PostgreSQL
```

---

# Connection Established

Application now has:

```text
Backend
↓
Database
```

communication.

---

# Step 15 - Application Becomes Live

Render exposes application URL.

Example:

```text
https://teamvault.onrender.com
```

Users can now access application.

Deployment complete.

---

# What Happens On Every Push?

Suppose I change:

```text
One Line Of Code
```

and push.

Entire process repeats:

```text
Push
↓
GitHub Actions
↓
Build Image
↓
Push Image
↓
Render Pulls Image
↓
New Container
↓
Application Updated
```

---

# Why This Is Powerful

Without CI/CD:

```text
Build Manually

Push Manually

Deploy Manually
```

Every update.

---

With CI/CD:

```text
git push
```

and everything else happens automatically.

---

# TeamVault Example

Real Deployment Flow:

```text
Laptop
↓
Spring Boot Source Code
↓
GitHub
↓
GitHub Actions
↓
Build Docker Image
↓
Push To Docker Hub
↓
Render Pulls Image
↓
Create Container
↓
Inject Environment Variables
↓
Spring Boot Starts
↓
Connect PostgreSQL
↓
Application Live
```

---

# Common Beginner Misunderstandings

## GitHub Stores Images

Wrong.

GitHub stores:

```text
Source Code
```

Docker Hub stores:

```text
Images
```

---

## Docker Hub Runs Applications

Wrong.

Docker Hub stores images.

Render runs containers.

---

## Docker Runs Spring

Not exactly.

Docker runs:

```bash
java -jar app.jar
```

Spring runs inside Java.

---

## Environment Variables Come From GitHub

Wrong.

Usually:

```text
Render
AWS
Railway
Koyeb
```

provides runtime environment variables.

---

# Complete Mental Model

If I only remember one thing:

```text
Code
↓
GitHub
↓
GitHub Actions
↓
Docker Image
↓
Docker Hub
↓
Render
↓
Container
↓
Spring Boot
↓
Database
↓
Live Application
```

Every deployment is simply moving my application through these stages until users can access it from the internet.

Once this flow makes sense, Docker, Docker Hub, GitHub Actions, Render, Spring Profiles, and Environment Variables stop feeling like separate topics and become parts of one deployment pipeline.

# Docker Hub and Registries

## Why I Am Writing This

After understanding:

* Dockerfiles
* Images
* Containers

I encountered another question:

> Where does a Docker Image live after it is built?

Suppose I build TeamVault:

```bash
docker build -t teamvault .
```

Now I have an image.

Questions:

* How does Render get it?
* How do teammates get it?
* How do CI/CD pipelines use it?
* How can I run the same image on another machine?

This document answers those questions.

---

# The Problem

Suppose I build an image:

```text
teamvault
```

on my laptop.

The image exists only on:

```text
My Laptop
```

Nobody else can access it.

Not:

* Render
* AWS
* GitHub Actions
* Other developers

The image is trapped on my machine.

---

# Similar Problem In Git

Before GitHub:

```text
Source Code
```

existed only on:

```text
My Laptop
```

To share it:

* USB drive
* Zip file
* Email

were required.

GitHub solved this problem.

---

# Docker Has The Same Problem

Docker Images also need a storage location.

Need somewhere to store:

```text
Built Images
```

so they can be downloaded later.

---

# What Is A Docker Registry?

A Docker Registry is:

```text
A storage system for Docker Images.
```

Think:

```text
GitHub
=
Stores Source Code

Docker Registry
=
Stores Docker Images
```

---

# What Is Docker Hub?

Docker Hub is the most popular Docker Registry.

Think:

```text
GitHub for Docker Images
```

It allows developers to:

* Upload Images
* Download Images
* Version Images
* Share Images

---

# What GitHub Stores

GitHub stores:

```text
Java Files
README
Source Code
pom.xml
```

Example:

```text
TeamVault Repository
```

contains:

```text
src/
pom.xml
README.md
```

---

# What Docker Hub Stores

Docker Hub stores:

```text
Built Images
```

Example:

```text
teamvault:v1.0
```

contains:

```text
Java Runtime
Spring Boot JAR
Dependencies
Configuration
```

---

# The Typical Flow

Developer:

```text
Source Code
↓
Dockerfile
↓
Docker Image
↓
Docker Hub
```

Server:

```text
Docker Hub
↓
Download Image
↓
Run Container
```

---

# Building An Image

Example:

```bash
docker build -t teamvault .
```

Result:

```text
teamvault image
```

exists locally.

Still not available to anyone else.

---

# Logging Into Docker Hub

Before pushing images:

```bash
docker login
```

Docker asks for:

```text
Username
Password or Access Token
```

Once authenticated:

```text
You can push images.
```

---

# Image Naming Convention

Docker Images generally follow:

```text
username/image-name:tag
```

Example:

```text
nitinjha/teamvault:v1.0
```

Where:

```text
nitinjha
```

is Docker Hub username.

```text
teamvault
```

is image name.

```text
v1.0
```

is version tag.

---

# Tagging Images

Suppose image currently exists as:

```text
teamvault
```

Tag it:

```bash
docker tag teamvault nitinjha/teamvault:v1.0
```

Now Docker knows:

```text
This image belongs to Docker Hub repository:
nitinjha/teamvault
```

---

# Pushing Images

Upload image:

```bash
docker push nitinjha/teamvault:v1.0
```

Docker uploads image to Docker Hub.

Now it is available online.

---

# Pulling Images

Another machine can download:

```bash
docker pull nitinjha/teamvault:v1.0
```

No source code needed.

No Maven needed.

No compilation needed.

Just download image.

---

# Running Pulled Images

After download:

```bash
docker run nitinjha/teamvault:v1.0
```

Container starts.

Exactly the same image.

Exactly the same environment.

---

# Public Images

Example:

```bash
docker pull postgres:17
```

Question:

Where does this come from?

Answer:

Docker Hub.

The PostgreSQL team uploaded it.

You simply download it.

---

# Why We Rarely Build Databases Ourselves

Instead of:

```text
Create PostgreSQL Dockerfile
Build PostgreSQL
Publish PostgreSQL
```

we use:

```bash
docker pull postgres:17
```

because official images already exist.

---

# Private Images

Docker Hub supports:

## Public Repositories

Anyone can:

```text
Pull Image
```

---

## Private Repositories

Only authorized users can:

```text
Pull Image
```

Useful for company applications.

---

# Registries Other Than Docker Hub

Docker Hub is not the only registry.

Popular alternatives:

* GitHub Container Registry (GHCR)
* Amazon Elastic Container Registry (ECR)
* Google Artifact Registry
* Azure Container Registry

All solve the same problem:

```text
Store Docker Images
```

---

# Why Companies Use Their Own Registries

For personal projects:

```text
Docker Hub
```

is usually enough.

Large companies often prefer:

```text
AWS ECR

or

Google Artifact Registry
```

because infrastructure stays inside one cloud provider.

---

# How Render Uses Docker Images

Render supports two deployment models.

---

## Model 1

Render Builds Everything

```text
GitHub
↓
Render
↓
Build Image
↓
Run Container
```

Simplest option.

Good for personal projects.

---

## Model 2

CI/CD Builds Image

```text
GitHub
↓
GitHub Actions
↓
Docker Hub
↓
Render
↓
Run Container
```

More professional.

More scalable.

More moving parts.

---

# Why Push To Docker Hub If Render Can Build?

This confused me during TeamVault deployment.

Question:

```text
Why not just let Render build?
```

Answer:

Building consumes:

* CPU
* Memory
* Time

If GitHub Actions builds image first:

```text
GitHub
↓
Build Image
↓
Docker Hub
```

then Render only needs:

```text
Download Image
↓
Run Container
```

which is faster and more efficient.

---

# TeamVault Deployment Example

Development:

```text
Code
↓
Dockerfile
↓
Image
```

Publishing:

```text
Image
↓
Docker Hub
```

Deployment:

```text
Render
↓
Pull Image
↓
Run Container
```

---

# Versioning Images

Avoid relying only on:

```text
latest
```

Use:

```text
v1.0
v1.1
v1.2
```

Example:

```bash
docker tag teamvault nitinjha/teamvault:v1.0
```

Benefits:

* Rollback support
* Version tracking
* Safer deployments

---

# Mental Model

If I only remember one thing:

```text
GitHub
=
Stores Source Code

Docker Hub
=
Stores Docker Images
```

Deployment Flow:

```text
Dockerfile
↓
Image
↓
Docker Hub
↓
Render
↓
Container
```

A Docker Registry has only one responsibility:

```text
Store Docker Images
```

so that other machines can download and run them.

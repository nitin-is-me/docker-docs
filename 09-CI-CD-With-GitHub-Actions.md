# CI/CD With GitHub Actions

## Why I Am Writing This

While deploying TeamVault, GitHub Actions suddenly appeared.

Antigravity created:

```text
.github/workflows/docker.yml
```

and I immediately got confused.

Questions I had:

* What is CI/CD?
* What is GitHub Actions?
* Why does GitHub build Docker Images?
* Why does GitHub log into Docker Hub?
* Why not build directly on Render?
* What is a workflow?
* What happens when I push code?

This document explains the entire process.

---

# The Problem

Suppose I make changes to TeamVault.

Example:

```text
Fix Bug
Add Feature
Improve UI
```

Now I want to deploy.

Without automation:

```text
Push Code
↓
Build Application
↓
Build Docker Image
↓
Push Docker Image
↓
Deploy
```

Every time.

Manually.

Gets annoying very quickly.

---

# What Is CI/CD?

CI/CD stands for:

```text
Continuous Integration

Continuous Deployment
```

Ignore the fancy names initially.

Think:

```text
Automatically perform repetitive deployment tasks.
```

---

# Continuous Integration (CI)

Whenever code changes:

```text
Run Tests

Build Project

Check For Errors
```

automatically.

Goal:

```text
Catch Problems Early
```

---

# Continuous Deployment (CD)

After code is verified:

```text
Deploy Automatically
```

instead of manually deploying.

Goal:

```text
Get Changes To Production Faster
```

---

# Without CI/CD

Imagine TeamVault.

Every update:

```text
Push Code

Open Terminal

Build Project

Build Docker Image

Login To Docker Hub

Push Image

Deploy
```

Manually.

---

# With CI/CD

Push Code:

```text
GitHub
↓
Workflow Starts
↓
Build
↓
Test
↓
Push Image
↓
Deploy
```

Automatically.

---

# What Is GitHub Actions?

GitHub Actions is GitHub's automation platform.

Think:

```text
If X happens

Do Y automatically
```

---

# Example

If:

```text
Code Is Pushed
```

Then:

```text
Build Docker Image
```

automatically.

---

# Workflow

A workflow is:

```text
A set of automated instructions.
```

Stored in:

```text
.github/workflows/
```

---

# Example Project Structure

```text
project/

.github/
└── workflows/
    └── docker.yml

src/

pom.xml
```

---

# Trigger

Every workflow starts with a trigger.

Example:

```yaml
on:
  push:
    branches:
      - main
```

Meaning:

```text
Whenever code is pushed
to main branch
```

run workflow.

---

# Simple Visualization

```text
Push Code
↓
GitHub Notices Push
↓
Workflow Starts
```

---

# Jobs

A workflow contains jobs.

Example:

```yaml
jobs:
```

Think:

```text
Major Tasks
```

Examples:

```text
Build

Test

Deploy
```

---

# Steps

Jobs contain steps.

Example:

```text
Build Job
    ↓
Checkout Code
    ↓
Setup Java
    ↓
Build Project
```

Each step performs one action.

---

# TeamVault Example

Typical flow:

```text
Push Code
↓
GitHub Actions
↓
Build Docker Image
↓
Push Docker Image
↓
Done
```

---

# Why GitHub Builds Docker Images

Question:

```text
Why not build on my laptop?
```

Because automation is better.

GitHub guarantees:

```text
Same Process

Every Time

Without Human Mistakes
```

---

# Why GitHub Needs Docker Hub Login

To push images.

Imagine:

```text
GitHub Creates Image
```

Question:

```text
Where should image go?
```

Answer:

```text
Docker Hub
```

---

# GitHub Must Authenticate

Exactly like:

```text
docker login
```

on your laptop.

GitHub also needs credentials.

---

# GitHub Secrets

Never hardcode:

```yaml
username: nitinjha
password: mypassword
```

inside workflow.

Bad.

Very bad.

😭

---

# Solution

GitHub Secrets.

Repository:

```text
Settings
↓
Secrets and Variables
↓
Actions
```

Store:

```text
DOCKER_USERNAME

DOCKER_PASSWORD
```

securely.

---

# Accessing Secrets

Example:

```yaml
${{ secrets.DOCKER_USERNAME }}
```

GitHub injects actual value.

Same idea as:

```text
Environment Variables
```

in Spring.

---

# Typical Docker Workflow

Step 1

Checkout code.

```yaml
- uses: actions/checkout@v4
```

Meaning:

```text
Download repository code
```

onto GitHub runner.

---

# Step 2

Login to Docker Hub.

```yaml
- uses: docker/login-action@v3
```

Meaning:

```text
Authenticate
```

with Docker Hub.

---

# Step 3

Build Image.

```yaml
docker build
```

creates:

```text
Docker Image
```

---

# Step 4

Push Image.

```yaml
docker push
```

uploads image.

---

# Complete Visualization

```text
GitHub Repository
↓
GitHub Actions
↓
Build Docker Image
↓
Login Docker Hub
↓
Push Docker Image
↓
Docker Hub
```

---

# Why This Is Useful

Without CI/CD:

```text
Developer Must Build Image
```

Every deployment.

---

With CI/CD:

```text
Push Code
↓
Everything Happens Automatically
```

---

# Why Companies Love CI/CD

Benefits:

```text
Consistency

Automation

Reliability

Speed
```

No forgotten deployment steps.

No manual mistakes.

---

# Why Antigravity Generated It

Antigravity wanted:

```text
Production Style Workflow
```

instead of:

```text
Build Everything Manually
```

---

# Render Build Strategy

Render supports:

## Option 1

Render Builds.

```text
GitHub
↓
Render
↓
Build
↓
Run
```

Simple.

Good for small projects.

---

## Option 2

GitHub Builds.

```text
GitHub
↓
GitHub Actions
↓
Docker Hub
↓
Render
↓
Run
```

More professional.

Common in industry.

---

# Why Companies Prefer Option 2

Building consumes:

```text
CPU

Memory

Time
```

If GitHub builds image first:

```text
Render only downloads image.
```

Less work.

Faster deployments.

More predictable.

---

# TeamVault Example

My TeamVault Pipeline:

```text
Push Code
↓
GitHub Actions
↓
Build Docker Image
↓
Push To Docker Hub
↓
Render Pulls Image
↓
Container Starts
↓
Application Live
```

---

# GitHub Actions Runner

Question:

```text
Where does workflow run?
```

Answer:

```text
GitHub Runner
```

Think:

```text
Temporary Virtual Machine
```

created by GitHub.

Workflow executes there.

After completion:

```text
Runner Destroyed
```

---

# Real Mental Model

GitHub Actions is basically:

```text
A Temporary Cloud Computer
```

that GitHub gives me for a few minutes.

It:

```text
Downloads Code

Builds Project

Builds Image

Pushes Image
```

then disappears.

---

# CI vs CD

CI:

```text
Build

Test

Validate
```

CD:

```text
Deploy
```

Think:

```text
CI
=
Make Sure Code Is Good

CD
=
Get Code To Production
```

---

# Mental Model

If I only remember one thing:

```text
Push Code
↓
GitHub Actions
↓
Build Docker Image
↓
Push To Docker Hub
↓
Render Pulls Image
↓
Container Starts
```

GitHub Actions exists to:

```text
Automate Repetitive Tasks
```

so developers don't have to manually build and deploy every time.

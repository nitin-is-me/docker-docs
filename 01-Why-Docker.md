# Why Docker Exists

## Why I Am Writing This

When I first started learning Docker, I was overwhelmed with terms like:

* Dockerfiles
* Images
* Containers
* Docker Compose

> Why does Docker even exist?

This document focuses entirely on that question.

Before learning Docker commands, I should understand the problem Docker solves.

---

# Life Before Docker

Imagine I build TeamVault.

My laptop contains:

```text
Java 21
Maven 3.9
PostgreSQL 17
Ubuntu Linux
```

I write code.

Everything works.

I test everything.

No issues.

I push my code to GitHub.

---

Now another developer clones my repository.

Their machine:

```text
Java 17
Windows
PostgreSQL 15
```

Suddenly:

```text
Application crashes.
```

Why?

Because software rarely depends only on code.

Software also depends on:

```text
Operating System
Java Version
Database Version
Libraries
Configuration
Environment Variables
```

---

# The Famous Problem

Developers used to say:

```text
"It works on my machine."
```

This became one of the most famous jokes in software engineering.

Example:

Developer:

```text
Everything works.
```

Production Server:

```text
Everything is broken.
```

Developer:

```text
But it works on my machine...
```

😭

---

# Why Source Code Is Not Enough

Suppose I push TeamVault to GitHub.

GitHub contains:

```text
Java Files
Configuration Files
README
```

But GitHub does NOT contain:

```text
Installed Java
Operating System
PostgreSQL Installation
Environment Setup
```

Another machine must recreate all of that.

Sometimes:

```text
Correctly
```

Sometimes:

```text
Incorrectly
```

This causes bugs.

---

# Traditional Solution: Documentation

Before Docker, developers often wrote:

```md
Install Java 21

Install Maven

Install PostgreSQL

Create Database

Create User

Run These Commands

Set Environment Variables

Run Application
```

This works.

But humans make mistakes.

---

# Problem With Documentation

Imagine a setup guide with:

```text
20 installation steps
```

Even if each step has:

```text
99% success rate
```

The overall success rate becomes much lower.

Somebody will:

```text
Skip a step
Use wrong version
Miss configuration
```

Eventually something breaks.

---

# Enter Docker

Docker says:

```text
Stop sharing setup instructions.

Share the setup itself.
```

This is the key idea.

---

# The Containerization Idea

Instead of sharing:

```text
Source Code
+
Instructions
```

Docker shares:

```text
Source Code
+
Dependencies
+
Runtime
+
Configuration
```

as one package.

---

# Real World Analogy

Imagine shipping a car.

Old approach:

```text
Ship wheels
Ship engine
Ship seats
Ship instructions

Ask customer to assemble.
```

Docker approach:

```text
Ship complete car.
```

Customer just drives.

---

# What Docker Actually Packages

Docker can package:

```text
Application Code

Java Runtime

Installed Libraries

Dependencies

Configuration

Operating System Components
```

into one unit.

That unit is called:

```text
Docker Image
```

(covered later)

---

# What Docker Guarantees

Suppose Docker Image runs successfully on:

```text
My Laptop
```

The exact same image can run on:

```text
AWS
Render
Azure
Google Cloud
Another Developer's Laptop
```

without modification.

This is Docker's biggest advantage.

---

# Docker vs Virtual Machines

Many beginners confuse Docker with Virtual Machines.

They solve similar problems but work differently.

---

# Virtual Machine

A VM contains:

```text
Entire Operating System

Application

Dependencies
```

Example:

```text
Ubuntu
+ Java
+ PostgreSQL
+ TeamVault
```

Every VM includes its own OS.

---

# Docker Container

Docker containers share the host operating system.

Instead of:

```text
OS
+
Application
```

Docker mainly packages:

```text
Application
+
Dependencies
```

This makes containers:

```text
Smaller
Faster
More Efficient
```

than virtual machines.

---

# Visual Comparison

Virtual Machine:

```text
Physical Machine
    ↓
Host OS
    ↓
Hypervisor
    ↓
Guest OS
    ↓
Application
```

Docker:

```text
Physical Machine
    ↓
Host OS
    ↓
Docker Engine
    ↓
Container
```

Less overhead.

---

# Why Companies Love Docker

Docker provides:

```text
Consistency

Portability

Reproducibility

Isolation

Scalability
```

---

# Consistency

If something works in Docker:

```text
Development
Testing
Production
```

all use the same image.

Less surprises.

---

# Portability

Docker containers can move between:

```text
Laptop
Server
Cloud
```

without changes.

---

# Reproducibility

Anybody can reproduce the exact environment.

Example:

```text
Clone Repository

Run Docker

Done
```

No manual setup.

---

# Isolation

Applications do not interfere with each other.

Example:

Container A:

```text
Java 17
```

Container B:

```text
Java 21
```

Both can run on same machine.

Normally this would create conflicts.

Docker isolates them.

---

# Scalability

Suppose TeamVault becomes popular.

One instance:

```text
10 users
```

works.

But:

```text
10,000 users
```

need more servers.

Docker makes it easy to start additional copies.

This is one reason Docker became huge in cloud computing.

---

# How Docker Fits Into Deployment

Without Docker:

```text
Push Source Code
↓
Install Java
↓
Install Dependencies
↓
Configure Server
↓
Run Application
```

Many moving parts.

---

With Docker:

```text
Build Image
↓
Deploy Image
↓
Run Container
```

Much simpler.

---

# TeamVault Example

Without Docker:

```text
Install Java

Install Maven

Build Application

Configure Environment Variables

Run Jar
```

---

With Docker:

```text
docker run teamvault
```

Everything already packaged.

---

# Mental Model

If I only remember one thing from this document:

Docker exists to solve:

```text
"It works on my machine."
```

Instead of sharing:

```text
Code
+
Instructions
```

Docker shares:

```text
Code
+
Dependencies
+
Runtime
```

as one portable package.

That package behaves the same everywhere.

Everything else in Docker builds on top of this idea.

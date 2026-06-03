# Docker Networks and Volumes Explained

## Why I Am Writing This

After learning Docker Compose, I saw configurations like:

```yaml
services:

  backend:
    ...

  postgres:
    ...
```

and then:

```properties
jdbc:postgresql://postgres:5432/teamvault
```

My first question was:

> How does "postgres" become a hostname?

Then I saw:

```yaml
volumes:
  - postgres-data:/var/lib/postgresql/data
```

and wondered:

> Why is this needed?

This document explains Docker Networks and Volumes from first principles.

---

# Problem 1: How Do Containers Talk?

Suppose I have:

```text
Spring Boot Container

PostgreSQL Container
```

Both are running.

Question:

```text
How does Spring Boot connect
to PostgreSQL?
```

---

# Life Without Networking

Imagine:

Container A

```text
Spring Boot
```

Container B

```text
PostgreSQL
```

Without networking:

```text
They cannot see each other.
```

Each container is isolated.

---

# Isolation

Remember:

Docker provides:

```text
Isolation
```

This means:

Container A:

```text
Cannot automatically access
Container B.
```

Docker must provide a communication mechanism.

---

# Docker Networks

Docker Networks are virtual networks.

Think:

```text
Wi-Fi Network
```

for containers.

If two containers join the same network:

```text
They can communicate.
```

---

# Real World Analogy

Imagine:

```text
Container A
```

is a person.

```text
Container B
```

is another person.

If they are in:

```text
Different Cities
```

communication is difficult.

If they are in:

```text
Same Office
```

communication is easy.

Docker Networks create the office.

---

# What Compose Does Automatically

Compose automatically creates a network.

Example:

```yaml
services:

  backend:
    ...

  postgres:
    ...
```

Docker Compose secretly creates:

```text
Network
```

and places:

```text
backend

postgres
```

inside it.

---

# The Magic Hostname

Suppose:

```yaml
services:

  postgres:
```

Docker automatically creates:

```text
Hostname:

postgres
```

inside network.

---

Therefore:

Spring Boot can connect using:

```properties
jdbc:postgresql://postgres:5432/teamvault
```

because:

```text
postgres
```

resolves to:

```text
PostgreSQL Container
```

---

# Why localhost Fails

Many beginners write:

```properties
jdbc:postgresql://localhost:5432/teamvault
```

inside container.

This is wrong.

---

# Important Rule

Inside a container:

```text
localhost
```

means:

```text
This same container.
```

NOT:

```text
Other containers.
```

---

# Example

Backend Container:

```text
localhost
```

points to:

```text
Backend Container
```

itself.

It does NOT point to:

```text
PostgreSQL Container
```

---

# Correct Approach

Use service names.

Example:

```properties
jdbc:postgresql://postgres:5432/teamvault
```

where:

```text
postgres
```

is service name.

---

# Visualizing The Network

```text
+----------------------+
| Docker Network       |
|                      |
|  backend             |
|      ↕               |
|  postgres            |
|                      |
+----------------------+
```

Containers can talk because:

```text
Same Network
```

---

# Listing Networks

Command:

```bash
docker network ls
```

Example:

```text
bridge
host
teamvault_default
```

---

# Inspecting Networks

Command:

```bash
docker network inspect teamvault_default
```

Shows:

```text
Containers
IP Addresses
Configuration
```

inside network.

---

# Problem 2: Why Volumes Exist

Now let's discuss databases.

---

Suppose PostgreSQL container starts.

Database contains:

```text
Users

Projects

Articles
```

Everything works.

---

Now container crashes.

Or gets deleted.

---

Question:

```text
What happens to database?
```

Answer:

```text
Everything disappears.
```

😭

---

# Why Data Disappears

Containers are temporary.

Think:

```text
Container
=
Disposable
```

Delete container:

```text
Container data disappears.
```

---

# Real Example

Create PostgreSQL container.

Insert:

```text
100 Users
```

Delete container.

Create new PostgreSQL container.

Result:

```text
0 Users
```

Data lost.

---

# Docker Volume Solution

Docker Volumes provide:

```text
Persistent Storage.
```

Think:

```text
External Hard Drive
```

for containers.

---

# Without Volume

```text
Container
    ↓
Database Files
```

Delete container:

```text
Everything gone.
```

---

# With Volume

```text
Container
    ↓
Volume
```

Delete container:

```text
Volume survives.
```

New container:

```text
Reconnect to volume.
```

Data returns.

---

# Visual Example

Without Volume:

```text
PostgreSQL Container
    ↓
Database Files
```

Delete:

```text
Everything lost.
```

---

With Volume:

```text
PostgreSQL Container
        ↓
postgres-data Volume
```

Delete container:

```text
Volume remains.
```

Create new container:

```text
Reconnect volume.
```

Database survives.

---

# Compose Example

```yaml
services:

  postgres:
    image: postgres:17

    volumes:
      - postgres-data:/var/lib/postgresql/data
```

Meaning:

```text
Store PostgreSQL files
inside volume named:

postgres-data
```

---

# Named Volumes

Example:

```yaml
volumes:

  postgres-data:
```

Docker creates:

```text
Persistent Volume
```

named:

```text
postgres-data
```

---

# Listing Volumes

Command:

```bash
docker volume ls
```

Example:

```text
postgres-data
```

---

# Inspecting Volume

Command:

```bash
docker volume inspect postgres-data
```

Shows:

```text
Location
Configuration
```

of volume.

---

# Removing Volume

Command:

```bash
docker volume rm postgres-data
```

Warning:

```text
Deletes stored data.
```

😭

Be careful.

---

# TeamVault Example

Suppose TeamVault stores:

```text
Users

Projects

Articles

Memberships
```

inside PostgreSQL.

Without Volume:

```text
Restart container

Lose everything.
```

---

With Volume:

```text
Restart container

Keep everything.
```

Exactly what we want.

---

# Networks vs Volumes

Many beginners mix them up.

Networks:

```text
Allow communication.
```

Example:

```text
Backend
↔
Database
```

---

Volumes:

```text
Store persistent data.
```

Example:

```text
Database survives restart.
```

---

# Mental Model

If I only remember one thing:

Networks solve:

```text
How containers talk.
```

Volumes solve:

```text
How containers keep data.
```

Example:

```text
Backend
   ↓
Network
   ↓
Database
   ↓
Volume
   ↓
Stored Data
```

Without Networks:

```text
No communication.
```

Without Volumes:

```text
No persistence.
```

Every real application eventually needs both.

# Docker Commands Explained

## Why I Am Writing This

After understanding:

```text
Dockerfile
↓
Image
↓
Container
```

the next challenge is learning Docker commands.

The problem is that most tutorials throw commands without explaining:

* What they do
* When to use them
* What part of Docker they affect

This document explains Docker commands from first principles.

---

# Mental Model Before Learning Commands

Docker has three major things:

```text
Dockerfile
Image
Container
```

Commands usually affect either:

```text
Images
```

or

```text
Containers
```

Always ask:

```text
Am I working with an Image?

or

Am I working with a Container?
```

before running commands.

---

# Viewing Images

Command:

```bash
docker images
```

Shows:

```text
REPOSITORY     TAG       IMAGE ID
teamvault      latest    abc123
postgres       17        xyz789
```

Meaning:

```text
What images exist on my machine?
```

Think:

```text
Installed Docker packages.
```

---

# Building An Image

Command:

```bash
docker build -t teamvault .
```

Meaning:

```text
Read Dockerfile
↓
Create Image
↓
Name it teamvault
```

---

# Breaking Down The Command

docker build

```text
Build image.
```

---

-t

```text
Tag (name) image.
```

---

teamvault

```text
Image name.
```

---

.

```text
Current folder.
```

Docker looks for:

```text
Dockerfile
```

inside current directory.

---

# Running A Container

Command:

```bash
docker run teamvault
```

Meaning:

```text
Take image

↓

Create container

↓

Start container
```

---

# Why "run" Creates A Container

Many beginners think:

```bash
docker run
```

means:

```text
Start image.
```

Wrong.

Images cannot run.

Containers run.

Docker first creates a container.

Then starts it.

---

# Running In Detached Mode

Command:

```bash
docker run -d teamvault
```

Meaning:

```text
Run in background.
```

Without:

```bash
-d
```

terminal becomes occupied.

With:

```bash
-d
```

terminal remains usable.

---

# Naming Containers

Command:

```bash
docker run \
--name teamvault-app \
teamvault
```

Now container name becomes:

```text
teamvault-app
```

instead of random generated name.

---

# Viewing Running Containers

Command:

```bash
docker ps
```

Shows:

```text
Only running containers.
```

Example:

```text
CONTAINER ID
NAME
STATUS
PORTS
```

---

# Viewing All Containers

Command:

```bash
docker ps -a
```

Shows:

```text
Running
Stopped
Exited
```

containers.

---

# Stopping Containers

Command:

```bash
docker stop container_id
```

Example:

```bash
docker stop abc123
```

Meaning:

```text
Stop running container.
```

Container still exists.

---

# Starting Stopped Containers

Command:

```bash
docker start container_id
```

Example:

```bash
docker start abc123
```

Meaning:

```text
Restart existing container.
```

---

# Restarting Containers

Command:

```bash
docker restart container_id
```

Equivalent:

```text
Stop
↓
Start
```

in one command.

---

# Removing Containers

Command:

```bash
docker rm container_id
```

Meaning:

```text
Delete container.
```

Image remains.

---

# Why Image Still Exists

Remember:

```text
Image
=
Blueprint

Container
=
Running Instance
```

Deleting a container does not delete blueprint.

---

# Removing Images

Command:

```bash
docker rmi image_name
```

Example:

```bash
docker rmi teamvault
```

Meaning:

```text
Delete image.
```

---

# Viewing Logs

One of the most useful commands.

Command:

```bash
docker logs container_id
```

Example:

```bash
docker logs teamvault-app
```

Shows:

```text
Spring Boot Logs
Errors
Warnings
Output
```

---

# Following Logs Live

Command:

```bash
docker logs -f teamvault-app
```

Meaning:

```text
Continuously watch logs.
```

Equivalent to:

```bash
tail -f
```

for Docker.

---

# Executing Commands Inside Container

Command:

```bash
docker exec -it container_name bash
```

Example:

```bash
docker exec -it teamvault-app bash
```

Meaning:

```text
Open shell inside container.
```

Useful for debugging.

---

# Container Shell Analogy

Without exec:

```text
Outside container
```

With exec:

```text
Inside container
```

---

# Port Mapping

Most important command option.

---

Suppose Spring Boot runs:

```text
8080
```

inside container.

Container is isolated.

Need mapping.

---

Command:

```bash
docker run -p 8080:8080 teamvault
```

Meaning:

```text
Host Port 8080
↓
Container Port 8080
```

---

# Different Host Port

Command:

```bash
docker run -p 9000:8080 teamvault
```

Meaning:

```text
My Machine Port 9000
↓
Container Port 8080
```

Access:

```text
localhost:9000
```

---

# Environment Variables

Pass runtime configuration.

Example:

```bash
docker run \
-e DB_URL=mydb \
-e DB_PASSWORD=secret \
teamvault
```

Container receives:

```text
DB_URL
DB_PASSWORD
```

during startup.

---

# Listing Docker Networks

Command:

```bash
docker network ls
```

Shows:

```text
Available Docker Networks.
```

Covered later in Networks documentation.

---

# Listing Docker Volumes

Command:

```bash
docker volume ls
```

Shows:

```text
Available Docker Volumes.
```

Covered later in Volumes documentation.

---

# Downloading Images

Command:

```bash
docker pull postgres:17
```

Meaning:

```text
Download image
from Docker Hub.
```

---

# Uploading Images

Command:

```bash
docker push username/teamvault
```

Meaning:

```text
Upload image
to Docker Hub.
```

---

# Complete TeamVault Workflow

Build image:

```bash
docker build -t teamvault .
```

Run container:

```bash
docker run \
-d \
-p 8080:8080 \
--name teamvault-app \
teamvault
```

View logs:

```bash
docker logs -f teamvault-app
```

Stop:

```bash
docker stop teamvault-app
```

Start again:

```bash
docker start teamvault-app
```

Delete:

```bash
docker rm teamvault-app
```

---

# Commands I Will Use Most Often

Daily:

```bash
docker build

docker run

docker ps

docker logs

docker stop

docker start
```

Sometimes:

```bash
docker exec

docker rm

docker rmi
```

Rarely:

```bash
docker network

docker volume
```

---

# Mental Model

If I only remember one thing:

```text
Images
=
Stored Packages

Containers
=
Running Applications
```

Commands usually affect one of these two.

Ask:

```text
Am I working with Image?

or

Am I working with Container?
```

before running a command.

That immediately makes Docker commands easier to understand.

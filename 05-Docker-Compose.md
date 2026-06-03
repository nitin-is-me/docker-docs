# Docker Compose Explained

## Why I Am Writing This

After learning Docker Images and Containers, I ran into a new problem.

TeamVault doesn't need only:

```text id="34tt1s"
Spring Boot
```

It also needs:

```text id="c1pxzd"
PostgreSQL
```

Eventually it might need:

```text id="nn4jx8"
Redis
Frontend
Backend
Database
```

Running each container manually becomes annoying.

Docker Compose solves this problem.

---

# Life Without Docker Compose

Suppose TeamVault requires:

```text id="vmc94u"
Backend
PostgreSQL
```

Without Compose:

Start database:

```bash id="4xpvgo"
docker run postgres
```

Start backend:

```bash id="10jj3u"
docker run teamvault
```

Need ports:

```text id="4onw52"
5432
8080
```

Need networking.

Need environment variables.

Need startup order.

Everything becomes manual.

---

# Docker Compose Idea

Docker Compose says:

```text id="wwvq0u"
Describe your entire application
inside one file.
```

Then Docker handles the rest.

---

# What Is Docker Compose?

Docker Compose is a tool that lets us run:

```text id="h0vtsn"
Multiple Containers
```

as one application.

Instead of:

```text id="cxrrd4"
Start Container A

Start Container B

Configure Network

Configure Ports
```

we write one file.

---

# Compose File

Usually:

```text id="z3z6k8"
docker-compose.yml
```

or

```text id="uc1u4v"
compose.yml
```

---

# Example

```yaml id="6rwn1q"
services:

  backend:
    build: .

  postgres:
    image: postgres:17
```

This file describes:

```text id="aaxkzh"
Two containers.
```

---

# Important Concept

Docker Compose does not replace Docker.

Compose uses Docker underneath.

Think:

```text id="7p5b3n"
Docker
=
Engine

Compose
=
Convenience Layer
```

---

# Understanding Services

Everything in Compose is a service.

Example:

```yaml id="d07nli"
services:

  backend:

  postgres:
```

Here:

```text id="cbppjg"
backend
```

is one service.

```text id="r4vgjl"
postgres
```

is another service.

---

# Real TeamVault Example

```yaml id="c12r8j"
services:

  backend:
    build: .

  database:
    image: postgres:17
```

Meaning:

```text id="jmwn26"
Start TeamVault Backend

AND

Start PostgreSQL
```

together.

---

# build

Example:

```yaml id="kj0g5t"
backend:
  build: .
```

Meaning:

```text id="tf1knu"
Build image from Dockerfile
inside current directory.
```

Equivalent to:

```bash id="g4jlwm"
docker build .
```

---

# image

Example:

```yaml id="1dfrs0"
postgres:
  image: postgres:17
```

Meaning:

```text id="lupj5f"
Use existing image
from Docker Hub.
```

Equivalent to:

```bash id="tgs9j2"
docker pull postgres:17
```

---

# Ports

Example:

```yaml id="ny4sd0"
ports:
  - "8080:8080"
```

Meaning:

```text id="t6t5pd"
Host 8080
↓
Container 8080
```

---

# PostgreSQL Ports

Example:

```yaml id="0xj3vf"
ports:
  - "5432:5432"
```

Meaning:

```text id="l20c3v"
My Machine 5432
↓
PostgreSQL Container 5432
```

---

# Environment Variables

Example:

```yaml id="3r9lhf"
environment:
  DB_USERNAME: postgres
  DB_PASSWORD: password
```

Container receives:

```text id="w6mv8j"
DB_USERNAME
DB_PASSWORD
```

during startup.

---

# Full TeamVault Example

```yaml id="itfjlwm"
services:

  postgres:
    image: postgres:17

    environment:
      POSTGRES_DB: teamvault
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password

    ports:
      - "5432:5432"

  backend:
    build: .

    ports:
      - "8080:8080"

    environment:
      DB_URL: jdbc:postgresql://postgres:5432/teamvault
      DB_USERNAME: postgres
      DB_PASSWORD: password

    depends_on:
      - postgres
```

This launches:

```text id="jlwm0d"
PostgreSQL

AND

Spring Boot
```

together.

---

# Important Question

Why:

```text id="ryjtzn"
postgres
```

inside JDBC URL?

Example:

```text id="87kqvy"
jdbc:postgresql://postgres:5432/teamvault
```

Why not:

```text id="e6mpsg"
localhost
```

?

---

# Answer

Because containers communicate using service names.

Compose automatically creates networking.

Service:

```yaml id="dfkchv"
postgres:
```

gets hostname:

```text id="21z06o"
postgres
```

---

Backend can connect using:

```text id="eyr1h8"
postgres
```

instead of:

```text id="ylnbsp"
localhost
```

---

# depends_on

Example:

```yaml id="0yfcv8"
depends_on:
  - postgres
```

Meaning:

```text id="nfb20v"
Start postgres first.
```

Then:

```text id="s6xpf7"
Start backend.
```

---

# Starting Everything

Command:

```bash id="qvwf9n"
docker compose up
```

Meaning:

```text id="l1p6u6"
Read compose file

Create containers

Start containers
```

---

# Detached Mode

Command:

```bash id="rxd90d"
docker compose up -d
```

Meaning:

```text id="mfwnuh"
Run in background.
```

---

# Stopping Everything

Command:

```bash id="z7qtvw"
docker compose down
```

Meaning:

```text id="q6w6c3"
Stop all containers.

Remove all containers.
```

---

# Viewing Logs

Command:

```bash id="lgxk0x"
docker compose logs
```

Shows logs from:

```text id="aof4cv"
Backend

Database
```

together.

---

# Viewing Logs Live

Command:

```bash id="mrd9nf"
docker compose logs -f
```

Equivalent to:

```bash id="qbfq0g"
tail -f
```

for the entire application.

---

# Rebuilding After Changes

Suppose code changes.

Need new image.

Command:

```bash id="s2g7si"
docker compose up --build
```

Meaning:

```text id="50s3dl"
Rebuild image

Then start containers.
```

---

# Why Compose Is So Powerful

Without Compose:

```text id="15n5v6"
Start DB

Configure DB

Create Network

Start Backend

Configure Backend

Connect Backend To DB
```

Manually.

---

With Compose:

```bash id="86mwpv"
docker compose up
```

Done.

😭

---

# TeamVault Development Workflow

Start project:

```bash id="cwlzuh"
docker compose up -d
```

Check logs:

```bash id="nrgn7z"
docker compose logs -f
```

Stop everything:

```bash id="0u6b6j"
docker compose down
```

---

# Common Beginner Mistake

Using:

```text id="jlwmjs"
localhost
```

inside container communication.

Wrong:

```text id="8ux5h6"
jdbc:postgresql://localhost:5432/teamvault
```

Inside containers:

```text id="fn5h0w"
localhost
```

means:

```text id="5is9f7"
This same container.
```

Not another container.

Use:

```text id="vvr85y"
Service Name
```

instead.

---

# Mental Model

If I only remember one thing:

Docker runs:

```text id="zhzw3f"
One Container
```

Docker Compose runs:

```text id="5q3knd"
Entire Application Stack
```

Example:

```text id="3ih4v9"
Backend
Database
Redis
Frontend
```

all from one file.

Compose is what makes Docker practical for real projects.

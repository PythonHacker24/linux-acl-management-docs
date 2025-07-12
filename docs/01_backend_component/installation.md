---
order: 3
title: Installing Backend Server
summary: Installing the backend server in the Linux ACL Management System 
sidebar_title: Installation 
---

<br>

# Backend Quick Start

This guide will help you get up and running quickly, whether you’re developing locally or deploying in a containerized environment.

## Prerequisites

- Go (version 1.20+ recommended, for building with source code method)
- Docker and Docker Compose (for containerized setup only)
- PostgreSQL and Redis
- (Optional) Make (for using the Makefile)

## 1. Getting the Backend Component

### Through Prebuilt Binaries

The prebuilt binaries are available to install on GitHub Releases for the Linux ACL Management Project.

Here are one line commands to install them for all the architectures. Make sure you have `curl` installed.

### For Linux x86_64

```bash
curl -L https://github.com/example-org/aclm/releases/latest/download/aclm-linux-amd64 -o /usr/local/bin/aclm
chmod +x /usr/local/bin/aclm
```

### For Linux ARM64

```bash
curl -L https://github.com/example-org/aclm/releases/latest/download/aclm-linux-amd64 -o /usr/local/bin/aclm
chmod +x /usr/local/bin/aclm
```

### For Linux ARMv7

```bash
curl -L https://github.com/example-org/aclm/releases/latest/download/aclm-linux-amd64 -o /usr/local/bin/aclm
chmod +x /usr/local/bin/aclm
```

### Through GitHub Source Code

Visit the [Official GitHub Repository]() for Linux ACL Management System for the complete source code.

To clone the repository from GitHub, use the following command. Make sure you have `git` installed on your system.

```bash
git clone https://github.com/PythonHacker24/linux-acl-management-backend.git
```

## 2. Configuration

When working with the Linux ACL Management System, all you need is the backend binary, `config.yaml`, and `.env` setup with proper paramaters. With these 3 components, you are ready to deploy the backend component.

The `config.yaml` is the heart of the Linux ACL Management System and contains all you need to setup the system.

`.env` contains all the parameters that's meant to be server specific and kept secret (for example, database passwords, auth token parameters, etc.)

More about configuration here: *link to config*

## 3. Database Setup

Linux ACL Management System uses PostgreSQL and Redis for operations purposes. Make sure both of them are running and are configured to be used with the backend. If you’re running the database locally, create the database and apply migrations:

### PostgreSQL

It's recommended to follow the [Official PostgreSQL](https://www.postgresql.org/docs/) docs for setting it up. It can run on a seprate server or on the same server on which the backend is running. 

PostgreSQL is used as an archival database where sessions and transactions are stored when they are expired or the backend system is trigged to quit it's operation in middle of operation.

```bash
# Start PostgreSQL
sudo service postgresql start

# Create the database (example)
createdb your_db_name
```

After setting up the PostgreSQL database, you need to configure the schema for compatibility with the backend component. 

You just need to load up the following schema given [here](https://github.com/PythonHacker24/linux-acl-management-backend/blob/development-v1/db/schema.sql). You just have to copy paste the whole schema and execute it inside the database.

### Redis

Linux ACL Management System uses Redis for mirroring states of sessions and transactions for streaming on web dashboard. This allows the internal process scheduler to keep working on processing them without locking the resources for streaming on web dashboards frequently. When ever changes are made in sessions and transactions inside the backend system, the data is volunterly reflected in Redis which is ready to be served on frontend and any resource demanding it.  

For more information on installing Redis, refer the [Official Redis](https://redis.io/docs/latest/) docs.

```bash
# Start Redis
sudo service redis-server start
```

Alternatively, if you’re using Docker Compose, the services will be created automatically.

## 4. Build (only for users with source code)

There are currently 3 ways of installing Linux ACL Management System Backend Component. 

### Using Go

You can build and run the backend using Go directly, the Makefile, or Docker Compose.

Building through Go can be done through the Golang compiler with one command. Incase you don't have Go installed on your system, refer to the [Official Golang Docs](https://go.dev/doc/install).


```bash
go build -o bin/backend ./cmd/laclm
```

### Using Makefile

For installing via Makefile, make sure you have `make` installed and follow the commands listed below. The Makefile will take care of all the building procdures as per your system. Just make sure that all the required dependences are installed (like Go, protoc, etc.)

For installing `make`, follow the [Official Page](https://www.gnu.org/software/make/) for GNU's make project

```bash
make build
```

### Using Docker

For systems with docker installed and configured on them, you can use the `Dockerfile` for building the project image and running it. 

Docker Compose is meant to be a testing/development environment which loads the project image and pairs it up with OpenLDAP, PHPLdapAdmin, etc. 

**Current `docker-compose.yaml` not recommended for production usage.**

```bash
docker-compose up --build
```

Docker Compose will build the image, start the containers, and run the backend along with its dependencies.

!!! info "Note:"
    A complete Docker-Compose file with databases and default configuration for quick deployment is under development. This will allow users to deploy the complete backend components with a few commands.

## 5. Useful Commands

- **Build:** `make build`
- **Run:** `make run`
- **Test:** `make test`
- **Lint:** `make lint` (requires golangci-lint)
- **Clean:** `make clean`

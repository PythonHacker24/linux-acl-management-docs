---
order: 3
title: Installing File Server Daemons
summary: Installing the file server daemons in the Linux ACL Management System 
sidebar_title: Installation 
---

<br>

# ACL API Daemon

## 1. Getting the ACL API Daemon

### Through Prebuilt Binaries

The prebuilt binaries are available to install on GitHub Releases for the Linux ACL Management Project.

Here are one line commands to install them for all the architectures. Make sure you have curl installed.

#### For Linux x86_64

```bash
curl -L https://github.com/example-org/aclm/releases/latest/download/aclapi-linux-amd64 -o /usr/local/bin/aclapi
chmod +x /usr/local/bin/aclapi
```

#### For Linux ARM64

```bash
curl -L https://github.com/example-org/aclm/releases/latest/download/aclapi-linux-amd64 -o /usr/local/bin/aclapi
chmod +x /usr/local/bin/aclapi
```

### Through GitHub Source Code

Visit the [Official GitHub Repository](https://github.com/PythonHacker24/linux-acl-management-aclapi) for Linux ACL Management System for the complete source code.

To clone the repository from GitHub, use the following command. Make sure you have git installed on your system.

```bash
git clone https://github.com/PythonHacker24/linux-acl-management-aclapi
```

## 2. Configuration

When working with the Linux ACL Management System's ACL API Daemon Component, all you need is the `aclapi` binary, `aclapi.yaml` with proper paramaters.

The `aclapi.yaml` is the heart of the Linux ACL Management System ACL API Daemon and contains all you need to setup the system.

More about configuration here: **link to config**

## 3. Build

There are currently 2 ways of building Linux ACL Management System ACL API Daemon Component.

You can build and run the `aclapi` using Go directly or the Makefile.

### Using Go

Building through Go can be done through the Golang compiler with one command. Incase you don't have Go installed on your system, refer to the [Official Golang Docs](https://go.dev/doc/install).

```bash
go build -o bin/aclapi ./cmd/aclapi
```

### Using Makefile

For installing via Makefile, make sure you have `make` installed and follow the commands listed below. The Makefile will take care of all the building procdures as per your system. Just make sure that all the required dependences are installed (like Go, protoc, etc.)

For installing `make`, follow the [Official Page](https://www.gnu.org/software/make/) for GNU's make project

```bash
make build
```

## 4. Useful Commands

- **Build:** `make build`
- **Run:** `make run`
- **Test:** `make test`
- **Lint:** `make lint` (requires golangci-lint)
- **Clean:** `make clean`

<br>

# ACL Core Daemon

## 1. Getting the ACL Core Daemon

### Through Prebuilt Binaries

The prebuilt binaries are available to install on GitHub Releases for the Linux ACL Management Project.

Here are one line commands to install them for all the architectures. Make sure you have curl installed.

#### For Linux x86_64

```bash
curl -L https://github.com/example-org/aclm/releases/latest/download/aclcore-linux-amd64 -o /usr/local/bin/aclcore
chmod +x /usr/local/bin/aclcore
```

#### For Linux ARM64

```bash
curl -L https://github.com/example-org/aclm/releases/latest/download/aclcore-linux-amd64 -o /usr/local/bin/aclcore
chmod +x /usr/local/bin/aclcore
```

### Through GitHub Source Code

Visit the [Official GitHub Repository](https://github.com/PythonHacker24/linux-acl-management-aclcore) for Linux ACL Management System for the complete source code.

To clone the repository from GitHub, use the following command. Make sure you have git installed on your system.

```bash
git clone https://github.com/PythonHacker24/linux-acl-management-aclcore
```

## 2. Configuration

When working with the Linux ACL Management System's ACL Core Daemon Component, all you need is the `aclcore` binary, `aclcore.yaml` with proper paramaters.

The `aclcore.yaml` is the heart of the Linux ACL Management System ACL Core Daemon and contains all you need to setup the system.

More about configuration here: **link to config**

## 3. Build

There are currently 2 ways of building Linux ACL Management System ACL Core Daemon Component.

You can build and run the `aclcore` using Go directly or the Makefile.

### Using Go

Building through Go can be done through the Golang compiler with one command. Incase you don't have Go installed on your system, refer to the [Official Golang Docs](https://go.dev/doc/install).

```bash
go build -o bin/aclcore ./cmd/aclcore
```

### Using Makefile

For installing via Makefile, make sure you have `make` installed and follow the commands listed below. The Makefile will take care of all the building procdures as per your system. Just make sure that all the required dependences are installed (like Go, protoc, etc.)

For installing `make`, follow the [Official Page](https://www.gnu.org/software/make/) for GNU's make project

```bash
make build
```

## 4. Useful Commands

- **Build:** `make build`
- **Run:** `make run`
- **Test:** `make test`
- **Lint:** `make lint` (requires golangci-lint)
- **Clean:** `make clean`

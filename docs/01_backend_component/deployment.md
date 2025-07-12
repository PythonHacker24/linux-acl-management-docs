---
order: 5
title: Deploying the Backend Server
summary: Deploying the backend server in the Linux ACL Management System 
sidebar_title: Deployment 
---

<br>

# Deployment

Deploying the Linux ACL Management System can done via various ways. This ranges for options prioritizing security while others prefer fast pace deployment.

## Deploying via Source Code

This method is the most secure and manual way of deploying Linux ACL Management System. It is recommended for institutions deploying it in an isolated environment without internet connection by building the binaries on-site where it is intended to deploy.

### 1. Install the Source Code Tarball

To install the tarball, visit the official repository and follow the instructions.

Copy the following commands to install it via command line. Make sure you have `curl` installed on the system.

#### Linux AMD64 Architecture

```bash
curl -L -o laclm.tar.gz https://github.com/PythonHacker24/linux-acl-management-backend/raw/refs/heads/development-v1/build/laclm-linux-amd64-source.tar.gz
```

#### Linx ARM64 Architecture

```bash
curl -L -o laclm.tar.gz https://github.com/PythonHacker24/linux-acl-management-backend/raw/refs/heads/development-v1/build/laclm-linux-arm64-source.tar.gz
```

You may now ship the tarball to any system with no internet connection and you will be able to build from the source code there provided that you have proper build tools installed on the target machine.

### 2. Extract the Source Code Tarball

Extract source code from tarball with following command. Make sure you have `tar` installed on the system.

```bash
tar -xzf laclm.tar.gz
```

This will create a folder in your current directory. `cd` into that folder.

### 3. Build Locally

You can build the binary with the following command:

```bash
make build-offline
```

This will build the binary and store it at `/bin`.

### 4. Configure

With the source code, you get `config.yaml` which you can configure with. For more information related to configuration, refer [here]().

### 5. Deploy

For deploying the backend, execute the binary with `config.yaml`

```bash
./laclm --config <path_to_config>
```

!!! info "Note:"
    Make sure that you have `.env` in the same directory where laclm is executed with config.

## Deploying via Prebuilt Binaries

You can install prebuilt libraries from offical GitHub repository. 

Copy the following commands to install it via command line. Make sure you have `curl` installed on the system.

### 1. Download the Binary

#### Linux AMD64 Architecture

```bash
curl -L -o laclm https://github.com/PythonHacker24/linux-acl-management-backend/raw/refs/heads/development-v1/bin/laclm-linux-amd64
```

#### Linx ARM64 Architecture

```bash
curl -L -o laclm https://github.com/PythonHacker24/linux-acl-management-backend/raw/refs/heads/development-v1/bin/laclm-linux-amd64
```

### 2. Configure

With the source code, you get `config.yaml` which you can configure with. For more information related to configuration, refer [here]().

### 3. Deploy

For deploying the backend, execute the binary with `config.yaml`

```bash
./laclm --config <path_to_config>
```

!!! info "Note:"
    Make sure that you have `.env` in the same directory where laclm is executed with config.

## Deploying via Docker Ecosystem

!!! warning "Warning:"
    Docker Compose method of deployment is under development and must only be used in development mode. Most features won't work as expected.

### 1. Get the Source Code

```bash
git clone https://github.com/PythonHacker24/linux-acl-management-backend
```

### 2. Build Image

```bash
docker build -t laclm .
```

### 3. Run Docker Image

```bash
docker run --rm laclm
```

For development purposes, use Docker Compose to run a test LDAP server (OpenLDAP) and PHPLDAPAdmin.

```bash
docker-compose up --build
```

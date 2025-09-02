---
order: 5
title: Deploying the Daemons
summary: Deploying the file server daemons in the Linux ACL Management System 
sidebar_title: Deployment 
---

<br>

# Deployment

Deploying the Linux ACL Management System can done via various ways. This ranges for options prioritizing security while others prefer fast pace deployment.

Since deploying the Linux ACL Daemons Components require `aclapi` and `aclcore` daemons, it is recommended that you install them together. 

## Deploying via Source Code

This method is the most secure and manual way of deploying Linux ACL Management System. It is recommended for institutions deploying it in an isolated environment without internet connection by building the binaries on-site where it is intended to deploy.

### 1. Install the Source Code Tarball

To install the tarball, visit the official repository and follow the instructions.

Copy the following commands to install it via command line. Make sure you have `curl` installed on the system.

#### Linux AMD64 Architecture

**For ACl API Daemon:**

```bash
curl -L -o aclapi.tar.gz https://github.com/PythonHacker24/linux-acl-management-aclapi/raw/refs/heads/development-v1/build/aclapi-linux-amd64-source.tar.gz
```

**For ACL Core Daemon:**

```bash
curl -L -o aclcore.tar.gz https://github.com/PythonHacker24/linux-acl-management-aclcore/raw/refs/heads/development-v1/build/aclcore-linux-amd64-source.tar.gz
```

#### Linux ARM64 Architecture¶

**For ACl API Daemon:**

```bash
curl -L -o aclapi.tar.gz https://github.com/PythonHacker24/linux-acl-management-aclapi/raw/refs/heads/development-v1/build/aclapi-linux-arm64-source.tar.gz
```

**For ACL Core Daemon:**

```bash
curl -L -o aclcore.tar.gz https://github.com/PythonHacker24/linux-acl-management-aclcore/raw/refs/heads/development-v1/build/aclcore-linux-arm64-source.tar.gz
```

You may now ship the tarball to any system with no internet connection and you will be able to build from the source code there provided that you have proper build tools installed on the target machine.

### 2. Extract the Source Code Tarball

Extract source code from tarball with following command. Make sure you have `tar` installed on the system.

**For ACL API Daemon:**

```bash
tar -xzf aclapi.tar.gz
```

**For ACL Core Daemon:**
```bash
tar -xzf aclcore.tar.gz
```

This will create a 2 folders in your current directory. You need to build them in their own directory. 

### 3. Build Locally

You can build the binary with the following command:

**For ACL API Daemon:**
```bash
cd aclapi/
make build-offline
```

**For ACL Core Daemon:**
```bash
cd aclcore/
make build-offline
```

This will build the binary for the source code and store it at `/bin`. You should see `aclapi/bin/aclapi` and `aclcore/bin/aclcore` binaires respectively.

After building the binaries, move them to `/usr/local/bin/`.

**Move ACL API to `/usr/local/bin/`**
```bash
sudo cp ./bin/aclapi /usr/local/bin/
```

**Move ACL Core to `/usr/local/bin/`**
```bash
sudo cp ./bin/aclcore /usr/local/bin/
```

`/usr/local/bin/` is the location where our systemd service will load the binaries from to create daemons that will run in the background.

After completing this steps, you can jump to **Complete Daemon Component Setup** for steps.

## Deploying via Prebuilt Binaries

You can install prebuilt libraries from offical GitHub repository.

Copy the following commands to install it via command line. Make sure you have `curl` installed on the system.

#### Linux AMD64 Architecture

**ACL API Daemon:**
```bash
curl -L -o aclapi https://github.com/PythonHacker24/linux-acl-management-aclapi/raw/refs/heads/main/bin/aclapi-linux-amd64
```

**ACL Core Daemon:**
```bash
curl -L -o aclcore https://github.com/PythonHacker24/linux-acl-management-aclcore/raw/refs/heads/main/bin/aclcore-linux-amd64
```

#### Linux ARM64 Architecture¶

**ACL API Daemon:**
```bash
curl -L -o aclapi https://github.com/PythonHacker24/linux-acl-management-aclapi/raw/refs/heads/main/bin/aclapi-linux-arm64
```

**ACL Core Daemon:**
```bash
curl -L -o aclcore https://github.com/PythonHacker24/linux-acl-management-aclcore/raw/refs/heads/main/bin/aclcore-linux-arm64
```

After completing this steps, you can jump to **Complete Daemon Component Setup** for steps.

## Complete Daemon Component Setup 

At this point, you have a binary built/installed in your preferred way and we are ready to deploy it on the file server.

### 1. Configure

With the source code of ACL API and ACL Core, you get `aclapi.yaml` and `aclcore.yaml` which you can configure with. For more information related to configuration, refer [here]().

After you configure the binaries, move them to `/etc/laclm/` from which the systemd will load the configuration from and provide it to the binary.

First, create a directory called `/etc/laclm/`:

```bash
mkdir /etc/laclm/
```

Move the configuration files to `/etc/laclm/`

```bash
cp aclapi/aclapi.yaml /etc/laclm/
```

```bash
cp aclcore/aclcore.yaml /etc/laclm/
```

### 2. Change Ownership and Access Permissions for ACL Core 

Since `aclcore` is going to run as root, you need to change the ownership of it to root and provide 775 permissions to it.

```bash
sudo chown root:root /usr/local/bin/aclcore
sudo chmod 755 /usr/local/bin/aclcore
```

### 3. Create a group called `laclm`

Create a group called `laclm`.

```bash
sudo groupadd laclm
```

### 4. Add root user to `laclm` group

```bash
sudo usermod -a -G laclm root
```

### 5. Create a user called `aclapi` with no home, least privileges, and added into `laclm` group.

```bash
sudo useradd --system --no-create-home --shell /usr/sbin/nologin --groups laclm aclapi
```

### 6. Create service for ACL API Daemon

#### a. Create the systemd service file

```bash
sudo touch /etc/systemd/system/aclapi.service
```

#### b. Copy this into aclapi.service

```
[Unit]                                           
Description=ACL API Daemon
After=network.target
Requires=aclcore.service

[Service]
Type=simple

ExecStart=/usr/local/bin/aclapi --config /etc/laclm/aclapi.yaml

User=aclapi
Group=laclm

NoNewPrivileges=yes
ProtectSystem=strict
ProtectHome=yes
PrivateTmp=yes

Restart=on-failure

[Install]
WantedBy=multi-user.target
```

### 7. Create service for ACL Core Daemon

#### a. Create the systemd service file

```bash
sudo touch /etc/systemd/system/aclcore.service
```

#### b. Copy this into aclcore.service

```
[Unit]
Description=ACL Core Daemon
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/aclcore --config /etc/laclm/aclcore.yaml

User=root
Group=laclm

PrivateTmp=yes
ProtectSystem=full
ProtectHome=yes
NoNewPrivileges=yes

PrivateNetwork=yes

Restart=on-failure

[Install]
WantedBy=multi-user.target
```

### 8. Reload SystemD daemons

```bash
sudo systemctl daemon-reload
```

### 9. Enable aclcore service (optional: daemon will auto start when system is restarted)

```bash
sudo systemctl enable aclcore.service
```

### 10. Start aclcore service

```bash
sudo systemctl start aclcore.service
```

### 11. Check aclcore status

```bash
sudo systemctl status aclcore.service
```

### 12. Enable aclapi service (optional: daemon will auto start when system is restarted)

```bash
sudo systemctl enable aclapi.service
```

### 13. Start aclapi service

```bash
sudo systemctl start aclapi.service
```

### 14. Check aclapi status

```bash
sudo systemctl status aclapi.service
```

## Testing Deployment

To test if the daemons are working properly, you can do manual tests before proceeding. The backend, however, attempts to connect all the daemons when it begins, so any daemon deployment issues can be caught while deploying the backend. However, it's better to test them beforehand.

The ACL API Daemon exposes gRPC handler which you can interact with to test. You can use gRPC UI to connect to the ACL API Daemon and test it. 

Find gRPC UI [here](https://github.com/fullstorydev/grpcui). Install and configure it before proceeding.

Run the following command to connect to the ACL API Daemon's gRPC Handler: 

```bash
grpcui -plaintext <hostname>:<port> 
```

`hostname` is the IP of you machine which you testing device has access to and `port` is the network port you decided to use for the gRPC handler which is specified in `aclapi.yaml` (default is `6593`).

You will get a localhost link on the system you are testing. Navigate to the URL and select `PingService`. If you are able to ping, then ACL API Daemon is working fine. 

To test ACL Core Daemon, you need to select `ACLEntry.Service` and you can try to change ACL value of a file by filling the parameters. The schema can be found [here]().

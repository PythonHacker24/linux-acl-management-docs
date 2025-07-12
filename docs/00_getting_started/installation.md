title: Requirements
order: 2
summary: Overview of requirements of components of the system 

!!! warning "Warning:"
    Project is **under development** and requirements might change frequently! Given requirements are based on experience of developers and calculations. More accurate requirements will be listed as the project is adopted more.

<br>

# System Requirements

## Backend Component

To run the Linux ACL Management Backend, ensure your system meets the following requirements and has the necessary dependencies installed.

### Operating System

- Linux (recommended for production)
- macOS and Windows (for development/testing; some features may require adaptation)

### Hardware

- **Minimum:** 2 CPU cores, 2GB RAM (for development/testing)
- **Recommended:** 4+ CPU cores, 8GB+ RAM (for production, especially with large organizations or many users)

### Software Dependencies

- **Go (Golang)**
Version: 1.20 or higher (Go 1.24.x recommended)
Used to build and run the backend application.

- **Redis**
Used for transaction logging and session management.
Version: 6.x or higher recommended.

- **PostgreSQL**
Used for archival and persistent storage of transaction data.
Version: 13.x or higher recommended.

- **LDAP (OpenLDAP is used for testing)**
Required for authentication and user management.
Version: Any recent stable version.

- **Docker (optional for development purposes)**
For running the application and dependencies in containers.
Version: 20.x or higher.

- **Docker Compose (optional for development purposes)**
For orchestrating multi-container setups (app, Redis, OpenLDAP, etc.).
Version: 1.29+ or Docker Compose V2.

- **Bash & ACL utilities**
bash: Required in the container for scripting.
acl: Required for manipulating Access Control Lists on Linux filesystems.

- **Other Tools (for development)**
    - **make:** For building the project using the provided Makefile.
    - **jq:** For parsing JSON in test scripts.
    - **curl:** For API testing.

### Network & Ports

- **8080/tcp:** Default API server port
- **389/tcp, 636/tcp:** OpenLDAP
- **6379/tcp:** Redis
- **5432/tcp:** PostgreSQL
- **8090/tcp:** phpLDAPadmin (optional, for LDAP management UI)

### Filesystem

- Access to the target filesystems (e.g., NFS, BeeGFS) is required for ACL management.
- Ensure the backend has the necessary permissions to modify ACLs on the managed paths.

### Environment Variables

Sensitive configuration (e.g., Redis password, LDAP admin DN/password, JWT secret) should be provided via environment variables or a .env file.

### Optional: Docker Compose

For advanced testing, a BeeGFS/NFS environment can be spun up using the provided docker-compose.beegfs.yaml.

!!! info "Information:"
    Advanced playground with Docker Compose is under development for learning/development purposes

## ACL API Daemon

To run the Linux ACL Management API Daemon, ensure your system meets the following requirements and has the necessary dependencies installed. Following are the requirements for **production mode**.

### Operating System

- Linux (recommended for production)

### Hardware

- **CPU:** 1+ core (x86_64/ARM64)
- **RAM:** 256MB minimum (512MB+ recommended)
- **Disk:** 50MB free space (for binary, logs, and config)

### Software

- Systemd or compatible init system (for running as a service)
- OpenSSL (for TLS, if enabled)
- Network access to host gRPC server (for communication with backend)

### Configuration

- `aclapi.yaml` configuration file (must be present and properly configured)
- Certificates in cert/ directory (if using TLS)
- Proper permissions for log and config directories

---

Following are the requirements for **development mode**.

### Software
- Go 1.20+ (see go.mod for exact version)
- Make (for using provided Makefile)
- Git (for source control)

### Development Tools (Recommendations)
- golangci-lint (for linting)
- go test (for running tests)
- Any editor/IDE with Go support

### Other
- Access to test certificates (for local TLS testing)
- Sample configuration files (provided with the code in the official repository)

## ACL Core Daemon

To run the Linux ACL Management Core Daemon, ensure your system meets the following requirements and has the necessary dependencies installed. Following are the requirements for **production mode**.

### Operating System

- Linux (recommended for production)

### Hardware

- **CPU:** 1+ core (x86_64/ARM64)
- **RAM:** 256MB minimum (512MB+ recommended)
- **Disk:** 50MB free space (for binary, logs, and config)

### Software

- Systemd or compatible init system (for running as a service)
- Adequate permissions to change ACL permissions on behalf of root/users

### Configuration

- `aclcore.yaml` configuration file (must be present and properly configured)
- Proper permissions for log and config directories

---

Following are the requirements for **development mode**.

### Software
- Go 1.20+ (see go.mod for exact version)
- Make (for using provided Makefile)
- Git (for source control)

### Development Tools (Recommendations)
- golangci-lint (for linting)
- go test (for running tests)
- Any editor/IDE with Go support

### Other
- Access to test certificates (for local TLS testing)
- Sample configuration files (provided with the code in the official repository)

## Additional Guidelines for Daemons

- Both daemons are designed to be run as background services (systemd unit files are provided).
- For production, ensure proper firewall and security settings.
- For development, you may need to install additional Go tools as specified in the Makefile or README.

!!! info "Note:"
    The API and Core daemons are highly dependent on each other to complete the their purpose as permission modification daemons. It's a good practice to deploy them together for better configuration.

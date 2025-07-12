---
order: 4
title: Configuring the Backend Server
summary: Configuring the backend server in the Linux ACL Management System 
sidebar_title: Configuration
---

!!! info "Note:"
    You don't need to configure all the parameters in the `config.yaml`, although, essential paramaters are required which the binary will ask if not specified. Other than that, rest of the paramaters will be set to default. Order of the configs doesn't matter.

<br>

# Backend Configuration Reference

This document explains all the available configuration options for the backend service.  
Use this as a reference when editing your `.env` file or YAML configuration.

## App

General application settings.

| Key | Description | Type |
|------|-------------|------|
| `name` | Name of the application. | `string` |
| `version` | Current version of the application. | `string` |
| `debug_mode` | Whether to run in debug mode (shows more logs, verbose errors). | `bool` |
| `session_timeout` | User session timeout in hours. | `int` |
| `base_path` | Base path for mounted storage or working directory. | `string` |
| `max_workers` | Maximum number of concurrent worker processes. | `int` |

---

## Server 

Backend server deployment settings.

| Key | Description | Type |
|------|-------------|------|
| `host` | The host address the server binds to. Use `0.0.0.0` to bind to all interfaces. | `string` |
| `port` | The port on which the backend server runs. | `int` |

---

## Database 

Settings for databases and caching services.

| Key | Subkey | Description | Type |
|------|--------|-------------|------|
| `transaction_log_redis.address` |  | Redis server address for transaction logging. | `string` |
| `transaction_log_redis.password` |  | Password for the Redis instance (read from env). | `env` |
| `transaction_log_redis.db` |  | Redis database index. | `int` |
| `archival_postgres.host` |  | Hostname of the PostgreSQL server. | `string` |
| `archival_postgres.port` |  | PostgreSQL server port. | `int` |
| `archival_postgres.user` |  | Username for the PostgreSQL server. | `string` |
| `archival_postgres.password` |  | Password for the PostgreSQL server. | `string` |
| `archival_postgres.dbname` |  | Name of the PostgreSQL database. | `string` |
| `archival_postgres.sslmode` |  | SSL mode for PostgreSQL connection. | `string` |

---

## Logging

Logging output settings for the backend service.

| Key | Description | Type |
|------|-------------|------|
| `file` | Path to the log file. | `string` |
| `max_size` | Maximum size of a single log file (in MB) before rotation. | `int` |
| `max_backups` | Maximum number of old log files to keep. | `int` |
| `max_age` | Maximum number of days to keep old log files. | `int` |
| `compress` | Whether to compress rotated log files. | `bool` |

---

## Filesystem Servers

List of managed filesystem servers.

| Key | Subkey | Description | Type |
|------|--------|-------------|------|
| `path` |  | Local path or mount point for the filesystem. | `string` |
| `method` |  | Connection method (e.g., `remote`). | `string` |
| `remote.host` |  | Remote server host address. | `string` |
| `remote.port` |  | Remote server port. | `int` |

---

## Authentication

Settings for authentication (LDAP).

| Key | Subkey | Description | Type |
|------|--------|-------------|------|
| `ldap.tls` |  | Whether to use TLS for LDAP connection. | `bool` |
| `ldap.address` |  | LDAP server address. | `string` |
| `ldap.admin_dn` |  | LDAP admin DN (read from env). | `env` |
| `ldap.admin_password` |  | LDAP admin password (read from env). | `env` |
| `ldap.search_base` |  | LDAP search base DN. | `string` |

---

## Backend Security

Security-related settings for tokens and sessions.

| Key | Description | Type |
|------|-------------|------|
| `jwt_secret_token` | Secret token used to sign JWTs (read from env). | `env` |
| `jwt_expiry` | JWT expiration time in hours. | `int` |

---

## Default Configuraton File

Here is the default backend configuration that can be used as an base template which works in development mode for testing purposes.

```yaml
# backend environment configs
app:
  name: 
  version: 
  debug_mode:
  session_timeout:
  base_path:
  max_workers:

# backend server deployment configs
server:
  host: 
  port: 

# databases for operations
database:
  transaction_log_redis: 
    address: 
    password: ${LACLM_TRANS_REDIS_PASSWORD}
    db: 
  archival_postgres:
    host: 
    port: 
    user: 
    password:
    dbname: 
    sslmode:

# logging configurations
logging:
  file: 
  max_size:
  max_backups:
  max_age:
  compress:

# filesystem server that needs management
filesystem_servers:
  - path: # PLACEHOLDER
    method:
    remote:
      host: # PLACEHOLDER 
      port: # PLACEHOLDER

# authentication information
authentication:
  ldap:
    tls:
    address:
    admin_dn: ${LACLM_LDAP_ADMIN_DN}
    admin_password: ${LACLM_LDAP_ADMIN_PASSWORD}
    search_base:

backend_security:
  jwt_secret_token: ${JWT_SECRET_TOKEN}
  jwt_expiry:

```

<br>

# Environment Variables

Given below is the environment variables file structure which contains server specific information and must be protected.

```
# Password for Redis Database 
LACLM_TRANS_REDIS_PASSWORD=

# LDAP Admin DN
LACLM_LDAP_ADMIN_DN=

# LDAP ADMIN PASSWORD
LACLM_LDAP_ADMIN_PASSWORD=

# JWT Secret Token
JWT_SECRET_TOKEN=
```

!!! info "Note:"
    The configuration mechanism is designed to make the setup replicable. You can write `config.yaml` once and use it to deploy multiple backends in similar environments. `.env` is intended to be server specific and needs to be modifed each time. This allows users to setup multiple servers with minimal config changes.

---
order: 4
title: Configuring the Backend Server
summary: Configuring the backend server in the Linux ACL Management System 
sidebar_title: Configuration
---

!!! info "Note:"
    You don't need to configure all the parameters in `config.yaml`. Essential parameters are required and the service will error if they are missing. All other parameters fall back to sensible defaults. Order of the configs doesn't matter.

<br>

# Backend Configuration Reference

This document explains all the available configuration options for the backend service.
Use this as a reference when editing your `config.yaml` and `.env`.

## App

General application settings.

| Key | Description | Type |
|------|-------------|------|
| `name` | Name of the application. Default: `laclm`. | `string` |
| `version` | Current version of the application. Default: `v1.1`. | `string` |
| `debug_mode` | Whether to run in debug mode (shows more logs, verbose errors). Default: `false`. | `bool` |
| `session_timeout` | User session timeout in hours. Default: `24`. | `int` |
| `base_path` | Base path for mounted storage or working directory. Required. | `string` |
| `max_workers` | Maximum number of concurrent worker processes. `0` lets the scheduler decide. | `int` |

---

## Server 

Backend server deployment settings.

| Key | Description | Type |
|------|-------------|------|
| `host` | The host address the server binds to. Default: `localhost`. Use `0.0.0.0` to bind to all interfaces. | `string` |
| `port` | The port on which the backend server runs. Default: `8080`. | `int` |

---

## Database 

Settings for databases and caching services.

| Key | Subkey | Description | Type |
|------|--------|-------------|------|
| `transaction_log_redis.address` |  | Redis address for transaction logging. Required. | `string` |
| `transaction_log_redis.password` |  | Password for the Redis instance (read from env). Optional. | `env` |
| `transaction_log_redis.db` |  | Redis database index. Default: `0`. | `int` |
| `archival_postgres.host` |  | Hostname of the PostgreSQL server. Default: `localhost`. | `string` |
| `archival_postgres.port` |  | PostgreSQL server port. Default: `5432`. | `int` |
| `archival_postgres.user` |  | Username for PostgreSQL. Required. | `string` |
| `archival_postgres.password` |  | Password for PostgreSQL (can be read from env). Optional but recommended. | `string/env` |
| `archival_postgres.dbname` |  | Name of the PostgreSQL database. Required. | `string` |
| `archival_postgres.sslmode` |  | SSL mode for PostgreSQL connection. Default: `disable`. | `string` |

---

## Logging

Logging output settings for the backend service.

| Key | Description | Type |
|------|-------------|------|
| `file` | Path to the log file. Default: `log/app.log`. | `string` |
| `max_size` | Maximum size of a single log file (in MB) before rotation. Default: `100`. | `int` |
| `max_backups` | Maximum number of old log files to keep. Default: `3`. | `int` |
| `max_age` | Maximum number of days to keep old log files. | `int` |
| `compress` | Whether to compress rotated log files. Default: `false`. | `bool` |

---

## Filesystem Servers

List of managed filesystem servers.

| Key | Subkey | Description | Type |
|------|--------|-------------|------|
| `path` |  | Local path or mount point for the filesystem. Required. | `string` |
| `method` |  | Connection method: `local` (default) or `remote`. | `string` |
| `remote.host` |  | Remote server host address. Required when `method` is `remote`. | `string` |
| `remote.port` |  | Remote server port. Required when `method` is `remote`. | `int` |

---

## Authentication

Settings for authentication (LDAP).

| Key | Subkey | Description | Type |
|------|--------|-------------|------|
| `ldap.tls` |  | Whether to use TLS for LDAP connection. Default: `false`. | `bool` |
| `ldap.address` |  | LDAP server address. Required. | `string` |
| `ldap.admin_dn` |  | LDAP admin DN (read from env). Required. | `env` |
| `ldap.admin_password` |  | LDAP admin password (read from env). Required. | `env` |
| `ldap.search_base` |  | LDAP search base DN. Required. | `string` |

---

## Backend Security

Security-related settings for tokens and sessions.

| Key | Description | Type |
|------|-------------|------|
| `jwt_secret_token` | Secret token used to sign JWTs (read from env). Required. | `env` |
| `jwt_expiry` | JWT expiration time in hours. Default: `24`. | `int` |

---

## Default Configuration File

Here is a backend configuration aligned with the repository's `backend/config.yaml` that works in development mode.

```yaml
# backend environment configs
app:
  name: laclm
  version: v1.1
  debug_mode: true
  session_timeout: 1
  base_path: /mnt
  max_workers: 5

# backend server deployment configs
server:
  host: 0.0.0.0
  port: 8080

# databases for operations
database:
  transaction_log_redis: 
    address: localhost:6379
    password: ${LACLM_TRANS_REDIS_PASSWORD}
    db: 0
  archival_postgres:
    host: localhost 
    port: 5432
    user: postgres
    password: ${PG_PASSWORD}
    dbname: postgres
    sslmode: disable

# logging configurations
logging:
  file: logs/app.log
  max_size: 100
  max_backups: 5
  max_age: 30
  compress: true

# filesystem servers that need management
filesystem_servers:
  - path: /nfs-system
    method: remote
    remote:
      host: localhost 
      port: 6593
  - path: /beegfs-system
    method: local

# authentication information
authentication:
  ldap:
    tls: false
    address: "ldap://localhost:389"
    admin_dn: ${LACLM_LDAP_ADMIN_DN}
    admin_password: ${LACLM_LDAP_ADMIN_PASSWORD}
    search_base: "cn=Princeton Plainsboro Hospital ,dc=myorg,dc=local"

backend_security:
  jwt_secret_token: ${JWT_SECRET_TOKEN}
  jwt_expiry: 1

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

# PostgreSQL password (if used in config.yaml)
PG_PASSWORD=

# JWT Secret Token
JWT_SECRET_TOKEN=
```

!!! info "Note:"
    The configuration mechanism is designed to make the setup replicable. You can write `config.yaml` once and use it to deploy multiple backends in similar environments. `.env` is intended to be server-specific and needs to be modified each time. This allows users to set up multiple servers with minimal config changes.

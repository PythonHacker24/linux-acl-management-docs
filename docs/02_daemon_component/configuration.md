---
order: 4
title: Configuring the Daemons 
summary: Configuring the file server daemons in the Linux ACL Management System 
sidebar_title: Configuration
---

<br>

# ACL API

## `daemon`

General settings for the ACLAPI Daemon process.

| Key | Description | Type |
|------|-------------|------|
| `debug_mode` | Enable debug mode (`true` for development, `false` for production). | `bool` |

---

## `logs`

Logging options for the daemon.

| Key | Description | Type |
|------|-------------|------|
| `file` | Path to the log file. | `string` |
| `max_size` | Maximum size (in MB) of a single log file before rotation. | `int` |
| `max_backups` | Maximum number of rotated log files to keep. | `int` |
| `max_age` | Maximum number of days to retain old log files. | `int` |
| `compress` | Whether to compress rotated log files. | `bool` |

---

## `server`

Server settings for the gRPC API.

| Key | Description | Type |
|------|-------------|------|
| `host` | Host address to bind the gRPC server. | `string` |
| `grpc_port` | Port on which the gRPC server listens. | `int` |
| `tls_enabled` | Enable TLS for the gRPC server (`true` recommended for production). | `bool` |
| `tls_cert_file` | Path to the TLS certificate file (required if `tls_enabled` is `true`). | `string` |
| `tls_key_file` | Path to the TLS key file (required if `tls_enabled` is `true`). | `string` |
| `tls_ca_cert_file` | Path to the CA certificate file (required if `tls_enabled` is `true`). | `string` |

---

## Config File Template

```yaml
# ACLAPI Daemon Configuration

# Daemon section
daemon:
  # Enable debug mode (true for development, false for production)
  debug_mode:

# Logging section
logs:
  # Log file path (default: /var/log/aclapi/aclapi.log)
  file:
  # Maximum size of a log file in MB before rotation (default: 100)
  max_size:
  # Maximum number of backup log files to keep (default: 3)
  max_backups:
  # Maximum age in days to retain old log files (default: 0, unlimited)
  max_age:
  # Compress rotated log files (default: false)
  compress:

# Server section
server:
  # Host address to bind the gRPC server (default: 0.0.0.0)
  host:
  # gRPC port to listen on (default: 6593)
  grpc_port:
  # Enable TLS for gRPC server (recommended: true in production)
  tls_enabled:
  # Path to TLS certificate file (required if tls_enabled is true)
  tls_cert_file:
  # Path to TLS key file (required if tls_enabled is true)
  tls_key_file:
  # Path to CA certificate file (required if tls_enabled is true)
  tls_ca_cert_file:

```

<br>

# ACL Core

## `Daemon`

General settings for the ACLCORE Daemon process.

| Key | Description | Type |
|------|-------------|------|
| `debug_mode` | Enable debug mode (`true` for development, `false` for production). | `bool` |
| `socket_path` | Path to the UNIX socket used by the daemon. | `string` |
| `max_conn_pool` | Maximum number of allowed pooled connections. | `int` |

---

## `Logs`

Logging options for the daemon.

| Key | Description | Type |
|------|-------------|------|
| `file` | Path to the log file. | `string` |
| `max_size` | Maximum size (in MB) of a single log file before rotation. | `int` |
| `max_backups` | Maximum number of rotated log files to keep. | `int` |
| `max_age` | Maximum number of days to retain old log files. | `int` |
| `compress` | Whether to compress rotated log files. | `bool` |

---

## Config File Template

```yaml
# ACLCORE Daemon Configuration

# Daemon section
daemon:
  # Enable debug mode (true for development, false for production)
  debug_mode:
  socket_path:
  max_conn_pool:

# Logging section
logs:
  # Log file path (default: /var/log/aclapi/aclapi.log)
  file:
  # Maximum size of a log file in MB before rotation (default: 100)
  max_size:
  # Maximum number of backup log files to keep (default: 3)
  max_backups:
  # Maximum age in days to retain old log files (default: 0, unlimited)
  max_age:
  # Compress rotated log files (default: false)
  compress:
```

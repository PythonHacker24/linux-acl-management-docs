---
order: 2
title: Daemons Architecture
summary: Deep dive into internal design of the file server daemon components and there places in the system 
sidebar_title: Architecture
---

<br>

# Architecture

When we talk about the daemon component, we consider 2 daemons working together: `aclapi` and `aclcore`.

A top level architecture of daemon component has been visualized below.

![](../img/daemon-arch.svg)

The `aclapi` exposes a gRPC handler which the backend component uses to communicate with it. The data that recieved through this is deserialized and sent to `aclcore` via Unix sockets. `aclcore` takes the instruction and executes it on the system via `setfacl`.

<br>

# ACL API gRPC Schema

You can find the protobuf files for `aclapi` in the source code [here](https://github.com/PythonHacker24/linux-acl-management-aclapi/tree/main/internal/grpcserver/protos).

### 1. ACL gRPC Service

This section describes the **ACLService** defined in `acl.proto` for managing Linux Access Control Lists (ACLs) via gRPC.

The **ACLService** provides an RPC method to apply ACL entries to files or directories.

#### RPC Method for ACLService
```proto
rpc ApplyACLEntry (ApplyACLRequest) returns (ApplyACLResponse);
```

#### Description

Applies an ACL entry (add, modify, or remove) to a given file or directory.

#### Messages

**ACLEntry** represents a single ACL Entry:

| Field         | Type     | Description                                                        |
| ------------- | -------- | ------------------------------------------------------------------ |
| `entity_type` | `string` | Type of the entity: `"user"`, `"group"`, `"mask"`, or `"other"`.   |
| `entity`      | `string` | Name of the user or group. Can be empty for `"other"` or `"mask"`. |
| `permissions` | `string` | Permissions in symbolic format, e.g., `"rw-"`.                     |
| `action`      | `string` | Operation to perform: `"add"`, `"modify"`, or `"remove"`.          |
| `is_default`  | `bool`   | Whether this entry is for the default ACL (`true` or `false`).     |

#### `ApplyACLRequest`

Request message for `ApplyACLEntry`.

| Field           | Type       | Description                                        |
| --------------- | ---------- | -------------------------------------------------- |
| `transactionID` | `string`   | Unique ID for tracking this request.               |
| `target_path`   | `string`   | Path to the file or directory to apply the ACL to. |
| `entry`         | `ACLEntry` | The ACL entry to apply.                            |

#### ApplyACLResponse

Response message for `ApplyACLEntry`.

| Field     | Type     | Description                                     |
| --------- | -------- | ----------------------------------------------- |
| `success` | `bool`   | Indicates whether the operation was successful. |
| `message` | `string` | Additional information or error message.        |

#### Example Usage

Below is an example of how a client might call the `ApplyACLEntry` RPC:

```json
{
  "transactionID": "abc123",
  "target_path": "/home/alice/docs",
  "entry": {
    "entity_type": "user",
    "entity": "alice",
    "permissions": "rw-",
    "action": "add",
    "is_default": false
  }
}
```

The response might look like:

```json
{
  "success": true,
  "message": "ACL entry added successfully."
}
```

### 2. Ping gRPC Service

The `PingService` provides a simple RPC to check if the server is reachable.

#### RPC Method for PingService

```proto
rpc Ping (PingRequest) returns (PingResponse);
```

#### Description

Sends a simple ping request to the server and expects a response message.

#### Messages

**Ping Request**

| Field     | Type      | Description                 |
| --------- | --------- | --------------------------- |
| *(empty)* | *(empty)* | This message has no fields. |

**Ping Response**

| Field     | Type     | Description                                    |
| --------- | -------- | ---------------------------------------------- |
| `message` | `string` | The server’s response message, e.g., `"pong"`. |

#### Example Usage

**Request**

```json
{}
```

**Response might look like**

```json
{
  "message": "pong"
}
```

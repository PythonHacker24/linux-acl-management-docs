---
order: 1
title: Quick Start 
summary: Getting started with the file server daemons 
sidebar_title: Quick Start 
---

<br>

# Introduction

The Daemon component is installed on Linux file servers which serves a file system like NFS which doesn't allow ACL changes to be applied on the mount point and requires manual modifications from the server's console itself. This daemon is managed by the backend component, which communicates with the daemon through gRPC framework.

This component is divided into 2 daemons, **ACL API Daemon** and **ACL Core Daemon**.

<br>

# ACL API Daemon

The ACL API daemon is responsible for communicated with the backend server, recieving ACL modification requests at it's gRPC endpoint, and comminicating with the ACL Core Daemon to modify the permissions as well as return the results of the operation back the the backend server.

It runs on the least privilege user for security purposes since it's exposed to the external network with gRPC.

Get the source code on the [Official GitHub Repository](https://github.com/PythonHacker24/linux-acl-management-aclapi).

<br>

# ACL Core Daemon

The ACL Core daemon is responsible for executing ACL modifications with `setfacl` and communicating with ACL API Daemon with Unix sockets.

It runs as a root user or as a user which has the permissions to execute `setfacl` on behalf of other users. It's protected from any other processes (for example, it has no access the any network interface).

Get the source code on the [Official GitHub Repository](https://github.com/PythonHacker24/linux-acl-management-aclcore).

title: Project Structure 
order: 1
summary: Understanding the structure of the project and it's various components 

<br>

# Introduction to Linux ACLs

Access Control Lists (ACLs) are very useful when managing access to various files on a Linux system. ACLs are used to provide a more flexible permission mechanism than the traditional owner/group/other model defined by standard Unix file permissions. With ACLs, you can define fine-grained permissions for multiple users and groups on a single file or directory.

For example, you might want to allow one specific user to have read-only access to a file while giving another user write access, without changing the file’s primary owner or group. ACLs make this possible by allowing you to add additional permission entries that override or extend the standard permission bits.

ACLs are managed using tools like `setfacl` to set or modify permissions and `getfacl` to view the current ACL settings. They are particularly useful in collaborative environments where different users or teams need different levels of access to the same files or directories. By using ACLs, system administrators can implement more precise access controls, enhancing both security and usability on shared systems.

<br>

# The Linux ACL Management System

The Linux ACL Management System provides an interface that allows users to modify ACL permissions on multiple Linux-based file servers. Institutions often have multiple file servers that provide a remote file system mounted on client machines, enabling users to access shared resources seamlessly. With the ACL Management System, administrators and authorized users can centrally manage detailed file and directory permissions across these servers, ensuring that individuals and groups have the appropriate level of access. This helps maintain security, simplifies permission management in complex environments, and supports collaborative work by allowing fine-grained control over who can read, write, or execute specific files, regardless of the underlying server structure.

Ideally, institutions have mutliple Linux servers which hosts filesystems like NFS, BeeGFS, etc. where files are stored by multiple users and they need to share access of the files with other users and collaborators. 

The Linux ACL Management System allows IT team to set up one unified management setup (for example, a single server or multiple servers working together) that can manage file permissions on multiple file servers on demand.

Linux ACL Management does this by utilizing 3 components, namely the backend, frontend, file server daemons. Each of them carry out a different responsibility and work together to make this possible.

**Show below is the high level architecture of the Linux ACL Management Project:**

![High Level Architecture](../img/complete-arch.svg)

## Backend Component

The backend component is responsible for managing sessions and transactions, serving endpoints for frontend to control it, communicate with remote daemon components for managing file system permissions, etc. 

It needs to be installed on a Linux server which has access to all the file system servers in the network and have all these file systems mounted on it (for example, /mnt/nfs-mount). 

### Terminologies

#### Sessions

Sessions are created by users who authenticate with the backend. Each session holds information related to the authenticated user and acts as a container for transactions that are scheduled and queued within it.

Every session has a unique Session ID, which serves as an identifier for tracking session details, the associated user, and all related transactions. In addition to transaction data, sessions also store user-specific metadata, such as IP address and browser information.

Each session is assigned an expiry timer. This timer ensures that a session automatically closes if there is no activity for a specified duration. However, the timer is paused while transactions are actively being processed, and resets once all transactions are complete. When a session expires, the user must authenticate again to create a new session.

This mechanism helps frontends automatically sign users out when inactive, improving security and freeing up backend resources by cleaning up unused sessions. It ensures efficient session lifecycle management while balancing user convenience and system performance.

#### Transactions

A transaction contains the ACL operation that a user wants to execute. For example, a transaction might specify that Aditya wants to grant Alice read and write permissions to `/data/mri-results/mri.scan`. Each transaction includes all the details required by the transaction executor to process the ACL command and return the result.

Every transaction has a unique Transaction ID and is linked to the Session ID of the session that created it. Using the Transaction ID, you can trace back to the session that scheduled the transaction, and ultimately to the user who initiated it, along with all related session and user information.

This structure ensures clear traceability, accountability, and accurate execution of ACL changes across the system.

### Components it depends on

#### Redis

[Redis](https://redis.io/) is an open-source, in-memory data store that can be used as a database, cache, and message broker. It is known for its high performance, low latency, and simplicity, making it ideal for real-time applications.

In this system, Redis acts as a mirror or intermediary for storing information about sessions and transactions. When streaming data to the frontend, the application fetches this information from Redis instead of directly querying the backend database.

This approach helps reduce resource locking in the backend, where sessions and transactions are actively processed. When the frontend requests data, it needs to receive a quick response, often requiring frequent and repeated reads whenever changes occur. If these reads were handled directly by the backend, they could lock critical resources each time, potentially degrading performance especially when many users request data simultaneously.

By using Redis, the backend can publish updates as soon as changes occur. The frontend can then fetch the required data from Redis as often as needed without placing additional load on the backend. This decouples read operations from the backend’s transactional workload, ensuring high responsiveness for users while maintaining backend performance. Redis’s efficiency in handling rapid reads and writes makes it an excellent fit for this use case.

#### PostgreSQL

[PostgreSQL](https://www.postgresql.org/) is a powerful, open-source, object-relational database management system known for its stability, advanced features, and standards compliance. It’s widely used for applications ranging from small web apps to large, mission-critical systems that require strong data integrity and scalability.

PostgreSQL in Linux ACL Management System is used as an archival database. When a session expires, it's complete information and all it's transactions information is stored into it.

In case where the backend is attempted to shutdown when active sessions exist and transactions are processing, the backend first expires all the sessions and stores all the information about session and transactions into this archive. Transactions that weren't processed are marked as `pending` and are stored in the archive. 

### Linux File Servers

#### BeeGFS File System

[BeeGFS](https://www.beegfs.io/c/) (BeeGFS stands for BeeGFS: the Berlin Exascale File System) is a high-performance, parallel file system designed for demanding environments like HPC clusters and research institutions. It allows large amounts of data to be distributed across multiple servers, providing fast access and scalability.

When BeeGFS is mounted on a Linux system (typically via a client node), files and directories appear as part of the local file system hierarchy. Linux ACL changes on BeeGFS-mounted directories can be executed just like on any other local file system using standard Linux ACL tools such as `setfacl` and `getfacl`. The Linux ACL Management System interacts with these tools and apply permission changes consistently, ensuring that access rights are enforced across all nodes and users accessing the shared BeeGFS storage.

#### NFS File System

NFS (Network File System) is a widely used protocol that allows files and directories to be shared and accessed over a network as if they were on the local machine.

Filesystems like NFS doesn't support running ACL permissions on mount points. In this case, Linux ACL Management System needs direct access to the host Linux file server where it can executed `setfacl` to modify ACLs. For this purpose, the backend utilizes the daemon component which executes `setfacl` on the demands of the backend securely.

## Daemons

The Daemon component is used by the backend component to execute `setfacl` on locally on remote Linux file servers. When the backend realises that the provided file by the user belongs to a file system which demands `setfacl` to be executed on locally, it communicates with the daemon and gets the command executed to make the ACL change.

The daemon component is divided into 2 Linux daemons: ACL API Daemon and ACL Core Daemon. Both of them are installed on the same system and they are highly dependent on each other to function as expected.

### ACL API Daemon

The API Daemon provides a gRPC endpoint for the backend to communicate with it and provides intructions about ACL modifications. This communication is done in very secure manner. It then decodes data provided by the backend server and forwards it to ACL Core daemon via Unix sockets.

The ACL API Daemon has very less privileges in the system to ensure security since it's exposed to the network.

### ACL Core Daemon

The ACL Core Daemon takes instructions from ACL API Daemon, executes `setfacl` on behalf of the user, and returns the response back. It is isolated from external network and communicated only with ACL API Daemon through Unix sockets.

## Frontend Component

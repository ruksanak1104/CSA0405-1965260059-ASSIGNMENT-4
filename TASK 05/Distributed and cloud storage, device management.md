
# Task 5.1 – Compare the Options 

A university requires all student and staff files to be accessible from any campus and from home.

## Comparison of Storage Systems

| Feature | NFS | HDFS / Ceph | Cloud Object Storage |
|---|---|---|---|
| Architecture | Network file system | Distributed storage | Object-based storage |
| Consistency | Strong semantics depend on configuration | Distributed consistency model | Provider-dependent |
| Availability | Depends on server configuration | High with replication | High |
| Performance | Good for shared file access | Good for distributed workloads | Good for large-scale access |
| Scalability | Limited by server architecture | Highly scalable | Highly scalable |
| Cost | Infrastructure and maintenance | Hardware and maintenance | Usage-based pricing |
| Access | Network-mounted files | Distributed access | API-based access |

## Evaluation

### 1. NFS (Network File System)

- Allows remote users to access files as if they were local.
- Suitable for shared university directories.
- Depends on network and server availability.
- Centralised storage can become a bottleneck.

### 2. HDFS (Hadoop Distributed File System)

- Stores large files across multiple machines.
- Supports data replication.
- Suitable for large-scale data processing.
- Not ideal for low-latency random updates to small files.

### 3. Ceph

- Provides distributed block, object and file storage.
- Supports replication and fault tolerance.
- Suitable for large institutional storage systems.

### 4. Cloud Object Storage

Examples: Amazon S3 and Google Cloud Storage.

- Provides scalable storage.
- Supports access from different geographical locations.
- Reduces the need for maintaining physical storage infrastructure.
- Costs depend on storage, requests and data transfer.

### Recommended Solution

A distributed file system such as Ceph combined with cloud object storage for backup and remote accessibility.

---

# Task 5.2 – Replication and Consistency 

Replication means maintaining multiple copies of data across different storage devices or servers.

## Example: Three-Copy Replication

```text
                 Original File
                      |
          -------------------------
          |           |           |
       Copy 1       Copy 2       Copy 3
       Server A     Server B     Server C
```

## Advantages

1. Protects data against hardware failure.
2. Improves data availability.
3. Allows recovery when one server fails.
4. Supports distributed access.

## Consistency Models

| Strong Consistency | Eventual Consistency |
|---|---|
| Updates are immediately visible after successful synchronisation | Updates may take time to reach all replicas |
| Easier to maintain a consistent view | Better availability in some distributed conditions |
| May increase write latency | May temporarily return older data |

## Evaluation

For important university records such as examination results and student details, strong consistency is preferable. For less critical content such as lecture videos and frequently accessed documents, eventual consistency may be acceptable.

### Trade-off

Three-copy replication improves reliability but increases storage requirements and network traffic.

---

# Task 5.3 – Device Management 

The operating system manages storage devices through the following mechanisms.

## 1. Device Drivers

Device drivers allow the operating system to communicate with hardware storage devices.

## 2. Buffering

Buffering temporarily stores data in memory during transfer between devices and processes.

## 3. Caching

Caching stores frequently accessed data in faster memory to reduce repeated disk access.

## 4. Spooling

Spooling queues operations so that devices can process requests in an organised manner.

## 5. Block Device Interface

Block devices transfer data in fixed-size blocks. The operating system uses this interface to manage disks and storage devices.

## Device Management Flow

```text
User Application
       |
       v
System Calls
       |
       v
File System
       |
       v
Block Device Interface
       |
       v
Device Driver
       |
       v
Storage Device
```

### Evaluation

Proper buffering, caching and device-driver management improve storage performance, reduce unnecessary disk operations and increase system efficiency.

---

# Alternative Concept and Complexity Analysis

If the university uses one large NFS server instead of a replicated distributed file system:

| Parameter | Single NFS Server | Three-Replica Distributed System |
|---|---|---|
| Storage capacity | One server | Multiple servers |
| Data copies | 1 | 3 |
| Read performance | Limited by server capacity | Can distribute reads |
| Write performance | Direct server writes | May require replica synchronisation |
| Availability | Single point of failure | Can tolerate some failures |
| Recovery | Requires backup | Replicas support recovery |
| Cost | Lower initial cost | Higher infrastructure cost |

## Storage Calculation

Assume the university stores 10 TB of files.

### Single-Copy Storage

\[
10 \text{ TB}
\]

### Three-Copy Replication

\[
10 \times 3 = 30 \text{ TB}
\]

### Additional Storage Required

\[
30 - 10 = 20 \text{ TB}
\]

## Conclusion

A distributed replicated storage system requires more storage but provides improved availability, fault tolerance and scalability.



# Task – Scalable Video Storage System for Online Lectures

## Problem Statement

A start-up streams lecture videos to 200,000 students. Videos range from 300 MB to 2 GB. Each video is uploaded once, while thousands of students may access the same video simultaneously. Students can begin playback at any minute.

Available resources include HDDs, SSDs, and cloud storage. The objective is to design an efficient, scalable, reliable, and cost-effective storage system.

---

# Stage 1 – Interpret the Workload 

## 1. Identify Operations

| Operation | Frequency | Access Pattern |
|---|---|---|
| Upload videos | Low | Sequential write |
| Store videos | Continuous | Sequential |
| Read videos | Very high | Sequential read |
| Search videos | High | Direct access |
| Update video metadata | Medium | Direct access |
| Delete old videos | Low | Direct access |

## 2. Access Patterns

- Videos are uploaded once.
- Video playback involves continuous sequential reading.
- Students may start at any minute, requiring random access to a playback position.
- Popular videos may be accessed simultaneously by thousands of users.
- The system must support concurrent requests efficiently.

## 3. Performance Targets

The system should provide:

1. Fast video-start time.
2. Continuous playback without buffering.
3. High availability.
4. Efficient storage utilisation.
5. Reliable backup and recovery.
6. Support for concurrent users.

### Testable Requirements

| Requirement | Performance Target |
|---|---|
| Video availability | 99.9% target |
| Playback start time | Under 2 seconds target |
| Concurrent access | Thousands of users |
| Data protection | Multiple copies |
| Video access | Sequential and random |
| Storage growth | Scalable |

> These are proposed design targets, not measured results.

---

# Stage 2 – Propose the Storage Architecture 

## Proposed Hybrid Storage Architecture

### Storage Distribution

| Storage Type | Purpose |
|---|---|
| SSD | Frequently accessed popular videos |
| HDD | Large video archive and less popular videos |
| Cloud object storage | Backup, scalability and additional storage |
| RAM cache | Frequently accessed metadata and video segments |

### File System

- XFS for high-performance Linux storage servers.
- Object storage for scalable video storage.
- A metadata database to store video information.

### Allocation Method

Indexed allocation or an object-storage block mapping system is recommended because it supports efficient access to video data and flexible storage management.

### Architecture Diagram

```text
                 STUDENTS
                     |
                     v
              APPLICATION SERVER
                     |
                     v
              METADATA DATABASE
                     |
                     v
                CACHE / CDN
                     |
                     v
                SSD STORAGE
                     |
                     v
             HDD / CLOUD STORAGE
```

### Working Process

1. A student requests a video.
2. The application server checks the metadata database.
3. The cache or CDN is checked for the requested video segment.
4. If available, the segment is delivered from the cache.
5. If unavailable, the video is fetched from SSD, HDD, or cloud storage.
6. Frequently accessed videos are retained in faster storage.

### Evaluation

Popular videos are placed in faster SSD or cache storage. Less frequently accessed videos remain on HDD or cloud storage.

---

# Stage 3 – Identify Critical Design Decisions 

## 1. Block Size

**Recommended block size: 1 MB**

### Reason

- Reduces metadata overhead.
- Supports large sequential video transfers.
- Provides efficient reading of video segments.

## 2. Disk Scheduler

- For HDDs, use an appropriate Linux I/O scheduler such as `mq-deadline` or `bfq`, depending on workload and device support.
- For SSDs, use `none` or `mq-deadline` after performance testing.

## 3. Caching Strategy

- Cache popular videos in SSDs.
- Cache frequently requested metadata in RAM.
- Use a CDN for geographically distributed users.
- Apply an LRU (Least Recently Used) policy to remove less frequently accessed cache entries.

## 4. Replication Factor

Use three copies of important video data across independent storage locations.

### Benefits

- Improves availability.
- Supports recovery after hardware failure.
- Reduces the risk of permanent data loss.

---

# Stage 4 – Design and Calculate

## A. Storage Capacity Calculation

Assume an average video size of 1 GB.

```text
Number of videos = 200,000

Total Storage = 200,000 × 1 GB
              = 200,000 GB
              = 200 TB
```

With three-copy replication:

```text
200 TB × 3 = 600 TB
```

Therefore, the required storage capacity is approximately **600 TB**, excluding metadata, overhead and additional backups.

## B. Throughput Calculation

Assume 10,000 students simultaneously watch videos.

```text
Average streaming bitrate = 5 Mbps

Total Throughput = 10,000 × 5 Mbps
                 = 50,000 Mbps
                 = 50 Gbps
```

The system must support approximately **50 Gbps** of aggregate video delivery under this assumed workload.

## C. Number of Disk Reads

Assume:

- Video size = 1 GB
- Block size = 1 MB

```text
Number of Blocks = 1024 MB / 1 MB
                 = 1024 blocks
```

A complete sequential read requires 1024 block reads, assuming one read per block.

If students start playback at different positions, the system must support efficient random access to the required video segments.

## D. Storage Allocation

| Storage | Percentage | Capacity |
|---|---:|---:|
| SSD | 20% | 40 TB |
| HDD | 60% | 120 TB |
| Cloud storage | 20% | 40 TB |
| **Total** | **100%** | **200 TB** |

The above represents the logical primary data distribution. Three-copy replication is accounted for separately.

## E. Performance Evaluation

| Parameter | Proposed Design |
|---|---|
| Storage | Hybrid SSD, HDD and cloud |
| File system | XFS |
| Allocation | Indexed/object-based |
| Block size | 1 MB |
| Caching | LRU |
| Replication | 3 copies |
| Disk scheduler | Device-dependent |

---

# Stage 5 – Implement and Demonstrate 

## Objective

Simulate video storage using two different storage designs and compare their performance.

### Design A: HDD-Based Storage

- All videos are stored on HDD.
- Every cache miss requires a disk read.
- Suitable for economical large-scale storage.

### Design B: Hybrid SSD and HDD Storage

- Frequently accessed videos are cached.
- Popular content is stored on SSD.
- Less frequently accessed content remains on HDD.



## Evaluation of Two Designs

| Parameter | Design A: HDD | Design B: Hybrid |
|---|---|---|
| Storage | HDD | SSD + HDD |
| Frequently accessed data | HDD | SSD/cache |
| Random access | Slower | Faster |
| Sequential access | Good | Good |
| Cost | Lower | Higher |
| Performance | Moderate | Better for popular content |

### Conclusion

The hybrid storage design improves access to popular videos by storing frequently requested content in SSD and cache storage. HDD and cloud storage provide economical capacity for less frequently accessed content.

---

# Alternative Concept and Complexity Analysis

## Alternative Design: Cloud Object Storage with CDN

Instead of storing videos on the company's own servers, all videos can be stored in cloud object storage and delivered through a Content Delivery Network (CDN).

| Parameter | Hybrid On-Premises | Cloud + CDN |
|---|---|---|
| Initial investment | High | Low |
| Scalability | Requires additional hardware | Highly scalable |
| Playback | Depends on server location | Delivered from nearby CDN locations |
| Storage | SSD and HDD | Cloud object storage |
| Maintenance | Managed by company | Managed by cloud provider |
| Data transfer cost | Internal network cost | Cloud bandwidth charges |
| Availability | Depends on infrastructure | Distributed infrastructure |

## Time Complexity

- Video metadata lookup: approximately **O(log n)** using a suitable indexed database.
- Sequential video reading: **O(n)** for n blocks.
- Cache lookup: **O(1)** using a hash-based cache.
- LRU cache update: **O(1)** using a hash map and doubly linked list.

## Storage Cost Comparison

For 200 TB of original videos:

- Single copy = 200 TB.
- Three copies = 600 TB.
- Cloud storage can eliminate the need to purchase and maintain all physical storage capacity, but introduces recurring storage and data-transfer charges.

---

# Final Recommendation

A hybrid architecture with SSD caching, HDD storage, cloud backup and CDN delivery is recommended.

It provides a balance between:

- Performance
- Availability
- Scalability
- Reliability
- Storage efficiency
- Operating cost


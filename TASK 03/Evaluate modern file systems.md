
# Task 3.1 – Compare Modern File Systems 

| Feature | FAT32 | NTFS | ext4 | XFS | Btrfs | ZFS | APFS |
|---|---|---|---|---|---|---|---|
| Maximum file size | 4 GB minus 1 byte | Very large | Very large | Very large | Very large | Very large | Very large |
| Journaling | No | Yes | Yes | Yes | Copy-on-write | Copy-on-write | Copy-on-write |
| Snapshots | No | Limited through VSS | No native snapshots | No native snapshots | Yes | Yes | Yes |
| Checksums | No general file-data checksums | Metadata checksums | Metadata checksums | Metadata checksums | Yes | Yes | Yes |
| Copy-on-write | No | No | No | No | Yes | Yes | Yes |
| Best use | USB compatibility | Windows systems | Linux general-purpose | Large Linux storage | Advanced Linux storage | Enterprise storage | Apple devices |

## Evaluation

- **FAT32:** Useful for compatibility but has a 4 GB individual file-size limit.
- **NTFS:** Provides security, journaling and reliable Windows file management.
- **ext4:** Provides good performance and reliability for Linux systems.
- **XFS:** Suitable for large files and high-throughput workloads.
- **Btrfs:** Supports snapshots, checksums and copy-on-write.
- **ZFS:** Provides advanced data integrity and storage management.
- **APFS:** Optimised for Apple's devices and flash storage.

### Recommendation

NTFS is suitable for Windows systems, ext4 for general Linux systems, and ZFS or Btrfs for systems requiring advanced data integrity and snapshot support.

---

# Task 3.2 – Journaling and Consistency 

Journaling is a file-system technique that records planned changes before applying them to the main file system.

## Working

1. The operating system receives a write request.
2. The changes are recorded in the journal.
3. The journal transaction is committed.
4. Changes are applied to the actual file system.
5. After a crash, the system replays committed transactions.

## Comparison of ext4 Journaling Modes

| Mode | Description | Performance | Protection |
|---|---|---|---|
| Journal | Both metadata and file data are journaled | Slower | Stronger protection |
| Ordered | Metadata is journaled after associated data is written | Balanced | Prevents stale data exposure through metadata |
| Writeback | Metadata is journaled without ordering data writes first | Faster | Weaker data consistency |

## Copy-on-Write (CoW)

Copy-on-write does not overwrite existing data immediately. Instead, it writes modified data to a new location and updates metadata to point to the new data.

### Comparison

- Journaling records changes before applying them.
- Copy-on-write preserves the old data until the new data and metadata are safely committed.
- CoW supports snapshots and can provide strong consistency.

---

# Task 3.3 – Choose for Each Device 

| Device | Recommended File System | Justification |
|---|---|---|
| Pen drive used on Windows and Linux | FAT32 or exFAT | Better cross-platform compatibility; exFAT supports large files |
| Linux database server | XFS | High performance and suitable for large files |
| Laptop requiring snapshots | Btrfs | Supports snapshots, checksums and rollback |

## Evaluation

- **exFAT:** Preferable to FAT32 when large files must be stored on a shared pen drive.
- **XFS:** Suitable for database workloads requiring high-throughput file operations.
- **Btrfs:** Suitable for laptops requiring snapshot-based recovery.

---

# Task 3.4 – Inspect a Real File System 

Execute the following commands on a Linux system.

## 1. Display File System Information

```bash
stat -f /
```

## 2. Display Disk Usage

```bash
df -i
```

## 3. Display Detailed File Information

```bash
ls -li
```

## 4. Display File System Information

```bash
tune2fs -l /dev/sda1
```

## 5. Display Block and Inode Information

```bash
dumpe2fs -h /dev/sda1
```

## Expected Evidence to Record

- Inode number
- Block size
- Total blocks
- Free blocks
- Total inodes
- Free inodes

---

# Alternative Concept and Complexity Analysis

If the database server used a copy-on-write file system such as Btrfs or ZFS instead of ext4 journaling:

| Parameter | ext4 | Btrfs / ZFS |
|---|---|---|
| Consistency | Journaling | Copy-on-write |
| Snapshots | Requires external support | Built-in |
| Checksums | Metadata checksums | Data and metadata checksums |
| Recovery | Journal replay | Consistent snapshots and metadata |
| Random writes | Generally efficient | Can incur CoW overhead |
| Storage overhead | Lower in many workloads | Additional snapshot and metadata space |

## Time Complexity

- **Ordinary indexed file lookup:** Approximately `O(log n)` with suitable indexing.
- **Snapshot creation:** Generally close to `O(1)` initially through metadata references.
- **Recovery:** Depends on journal or metadata size and the number of changes to replay.

## Conclusion

ext4 is suitable for general Linux workloads, while Btrfs and ZFS provide additional data-integrity and snapshot features for advanced storage environments.


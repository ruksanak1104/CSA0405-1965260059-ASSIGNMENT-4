
# Task 2.1 – Identify the Operations 
### Workload

A video-surveillance recorder stores footage from 32 cameras. Files continuously grow while recording, are mainly read sequentially, sometimes searched by time, and deleted after 30 days.

| Operation | Frequency | Access Pattern |
|---|---|---|
| Create video files | High | Sequential |
| Write video footage | Continuous | Sequential |
| Read recorded footage | High | Sequential |
| Search footage by time | Medium | Direct |
| Delete old footage | Periodic | Direct |
| Update file metadata | Medium | Direct |

### Observation

Sequential writing and reading are the dominant operations. However, searching recordings by time also requires efficient direct access.

---

# Task 2.2 – Compare the Allocation Methods 

| Feature | Contiguous | Linked (FAT) | Indexed |
|---|---|---|---|
| Sequential access | Excellent | Good | Good |
| Direct access | Excellent | Poor without FAT indexing | Excellent |
| File growth | Difficult | Easy | Easy |
| Fragmentation | External fragmentation | No external fragmentation | No external fragmentation |
| Reliability | Good | Depends on pointers/FAT | Good with index protection |
| Storage overhead | Low | Pointer/FAT overhead | Index block overhead |

### Evaluation

- Contiguous allocation provides excellent sequential access but makes file expansion difficult.
- Linked allocation supports file growth but requires following block pointers.
- FAT improves linked allocation by maintaining block links in a File Allocation Table.
- Indexed allocation supports both sequential and direct access but requires additional index blocks.

### Recommended Method

Indexed allocation is suitable because video files continuously grow and must support searching by time.

---

# Task 2.3 – Calculate the Cost 

### Given

- File size = 4 GB
- Block size = 4 KB
- Pointer size = 4 bytes
- Disk block size = 4 KB

## Step 1: Calculate the Number of Blocks

\[
\frac{4 \times 1024 \times 1024}{4}
= 1,048,576 \text{ blocks}
\]

## A. Contiguous Allocation

For direct access to the last block, the block address can be calculated using the starting block and offset.

- Data block reads for the last block: **1**
- Sequential traversal of the entire file: **1,048,576 block reads**

## B. Linked Allocation

To reach the last block sequentially, all previous blocks must be followed.

- Number of block reads = **1,048,576**

Without a FAT cache or auxiliary index, direct access to the last block also requires traversing the preceding links.

## C. Indexed Allocation

The last data block is located through the index structure.

- Index block read = 1
- Data block read = 1
- Total = **2 block reads** (assuming the index block is not cached).

---

## Maximum File Size Using Indexed Allocation

### Given

- 12 direct pointers
- 1 single-indirect pointer
- 1 double-indirect pointer
- 1 triple-indirect pointer
- Block size = 4096 bytes
- Pointer size = 4 bytes

### Number of Pointers per Index Block

\[
\frac{4096}{4}=1024
\]

### Maximum Number of Data Blocks

\[
12+1024+1024^2+1024^3
\]

\[
=1,074,791,436 \text{ blocks}
\]

### Maximum File Size

\[
1,074,791,436 \times 4096
\]

\[
=4,402,345,721,856 \text{ bytes}
\]

**Approximately 4.004 TiB.**

---

# Task 2.4 – Free-Space Management 

### Given

- Disk capacity = 4 TB
- Block size = 4 KB

Assuming binary units for the calculation:

### Total Number of Blocks

\[
\frac{4 \times 1024^3}{4}
=1,073,741,824 \text{ blocks}
\]

## Comparison of Free-Space Management Methods

| Method | Description | Advantage | Disadvantage |
|---|---|---|---|
| Bit vector | One bit per block | Easy to find free blocks | Requires bitmap storage |
| Linked free list | Free blocks contain pointers to next free blocks | Simple implementation | Slow to find consecutive blocks |
| Grouping | Stores addresses of multiple free blocks in one block | Faster free-block allocation | More complex management |

### Bit Vector Size

\[
\frac{1,073,741,824}{8}
=134,217,728 \text{ bytes}
\]

\[
=128 \text{ MiB}
\]

### Conclusion

Bit vectors are suitable for managing free space because they provide efficient tracking of allocated and available blocks.

---

## Explanation

- Files A, B, C and D are initially allocated.
- Files B and D are released.
- There are 21 free blocks, but they are divided into separate free regions.
- The first-fit contiguous allocation finds 10 consecutive blocks starting at block 19.
- Indexed allocation uses block 6 as the index block and blocks 7–13 and 29–31 as data blocks.

### Why Can't File E Be Stored in the First Free Region?

Although 21 blocks are free, the largest available continuous region contains only 13 blocks after the first allocation. In this particular layout, the first-fit algorithm finds a sufficient 13-block region starting at block 19. Indexed allocation can use non-contiguous free blocks and therefore supports flexible file growth.

---

# Alternative Concept and Complexity Analysis

If linked allocation with a File Allocation Table (FAT) stored in memory were used instead of indexed allocation:

| Parameter | FAT | Indexed Allocation |
|---|---|---|
| Sequential access | O(n) | O(n) |
| Direct access | O(1) with an in-memory FAT | O(1) for a cached index; otherwise index reads are required |
| Metadata | FAT entries | Index blocks |
| Memory usage | FAT table | Index block cache |
| Reliability | FAT corruption affects multiple files | Index corruption affects the corresponding file |

## FAT Memory Calculation

For a 4 TB disk with 4 KB blocks:

\[
1,073,741,824 \times 4
=4,294,967,296 \text{ bytes}
\]

\[
=4 \text{ GiB}
\]

A complete FAT containing 4-byte entries would require approximately **4 GiB of memory** if entirely loaded into RAM.

## Conclusion

Indexed allocation is preferred because it provides direct access and supports non-contiguous file growth without depending on a single FAT structure.


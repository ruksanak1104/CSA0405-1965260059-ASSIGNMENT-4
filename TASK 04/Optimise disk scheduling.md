
# Task 4.1 – Calculate 

### Given

- Cylinders: 0–199
- Initial head position: 53
- Request queue: 98, 183, 37, 122, 14, 124, 65, 67

## A. FCFS (First Come First Serve)

Requests are processed in their arrival order.

### Order

`53 → 98 → 183 → 37 → 122 → 14 → 124 → 65 → 67`

### Head Movement

\[
45+85+146+85+108+110+59+2
\]

\[
=640 \text{ cylinders}
\]

## B. SSTF (Shortest Seek Time First)

The closest pending request is selected at every step.

### Order

`53 → 65 → 67 → 37 → 14 → 98 → 122 → 124 → 183`

### Head Movement

\[
12+2+30+23+84+24+2+59
\]

\[
=236 \text{ cylinders}
\]

## C. SCAN

The head first moves towards cylinder 0 and then reverses direction.

### Order

`53 → 37 → 14 → 0 → 65 → 67 → 98 → 122 → 124 → 183`

\[
53+183=236 \text{ cylinders}
\]

## D. C-SCAN

The head moves towards cylinder 0, continues to cylinder 199, and then wraps around to cylinder 0 before servicing requests in ascending order.

### Order

`53 → 37 → 14 → 0 → 199 → 65 → 67 → 98 → 122 → 124 → 183`

Including the wrap movement:

\[
=504 \text{ cylinders}
\]

## E. LOOK

The head moves towards the lowest request and reverses at the last pending request rather than reaching cylinder 0.

### Order

`53 → 37 → 14 → 65 → 67 → 98 → 122 → 124 → 183`

\[
=208 \text{ cylinders}
\]

## F. C-LOOK

The head services requests in one direction and jumps from the lowest request to the highest pending request.

### Order

`53 → 37 → 14 → 183 → 124 → 122 → 98 → 67 → 65`

\[
=326 \text{ cylinders}
\]

## Comparison Table

| Algorithm | Total Head Movement |
|---|---:|
| FCFS | 640 |
| SSTF | 236 |
| SCAN | 236 |
| C-SCAN | 504 |
| LOOK | 208 |
| C-LOOK | 326 |

### Convention

The C-SCAN total includes the movement to the disk end and the wrap from 199 to 0. Some implementations report wrap movement separately. The C-LOOK total includes the circular jump.

### Evaluation

LOOK provides the lowest head movement for this particular request queue. FCFS has the highest movement because it processes requests without considering their physical locations.

---

# Task 4.2 – Simulate the Algorithms 

The following simulation can be used to compare FCFS, SSTF, SCAN and C-SCAN under different workloads.

## Simulation Requirements

- Generate 10,000 random requests.
- Test light, medium and heavy workloads.
- Calculate average head movement.
- Calculate maximum waiting time.
- Compare scheduling performance.

## Workload Classification

| Workload | Number of Requests |
|---|---:|
| Light | 1,000 |
| Medium | 5,000 |
| Heavy | 10,000 |


## Interpretation

- **FCFS:** Provides fair processing according to arrival order.
- **SSTF:** Reduces unnecessary head movement by selecting nearby requests.
- **SSTF:** May cause starvation when requests far away repeatedly get postponed.
- **Average movement:** Helps compare disk efficiency.
- **Maximum movement:** Provides an indication of the worst individual seek distance.

---

# Task 4.3 – Evaluate Fairness 

| Algorithm | Starvation | Fairness |
|---|---|---|
| FCFS | No | High |
| SSTF | Possible | Low |
| SCAN | Generally avoids starvation | Good |
| C-SCAN | Generally avoids starvation | Very good |

## Evaluation

- FCFS processes requests in arrival order, so every request eventually receives service.
- SSTF can starve requests located far from the current head position.
- SCAN moves in both directions and services requests along its path.
- C-SCAN services requests in one direction and provides more uniform waiting time across the disk.

## Why Does C-SCAN Provide More Uniform Waiting Time?

C-SCAN services requests in one direction only. After reaching the final cylinder, it returns to the beginning without servicing requests during the return. This creates a more uniform waiting-time distribution compared with SCAN, which services requests in both directions.

---

# Task 4.4 – Solid-State Drives 

SSDs do not contain moving mechanical disk heads. Therefore, seek time and rotational latency are almost eliminated.

Linux uses the `none` or `mq-deadline` I/O scheduler for SSDs because they provide efficient request handling without the need for traditional mechanical disk-head optimisation.

## Check the Active Scheduler

```bash
cat /sys/block/sda/queue/scheduler
```

## Explanation

- **none:** Minimal scheduling overhead.
- **mq-deadline:** Attempts to control request latency and prevent starvation.
- Traditional SCAN and SSTF algorithms are less useful for SSDs because there is no physical disk-head movement.

---



# Alternative Concept and Complexity Analysis

If the server used LOOK or C-LOOK instead of SCAN or C-SCAN:

| Parameter | SCAN | LOOK | C-SCAN | C-LOOK |
|---|---|---|---|---|
| Movement | To disk boundary | To last request | To disk boundary and wrap | Between extreme requests |
| Extra movement | Possible | Reduced | Higher | Reduced |
| Fairness | Good | Good | Very good | Very good |
| Implementation | Simple | Moderate | Moderate | Moderate |

## Complexity

- **FCFS:** `O(n)`
- **SSTF using a linear search:** `O(n^2)`
- **SCAN after sorting:** `O(n log n)`
- **C-SCAN after sorting:** `O(n log n)`
- **LOOK after sorting:** `O(n log n)`
- **C-LOOK after sorting:** `O(n log n)`

## Conclusion

For mechanical disks, LOOK or C-LOOK can reduce unnecessary head movement. For workloads requiring predictable waiting times, C-SCAN is a suitable choice.


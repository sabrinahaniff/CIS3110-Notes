---
title: Quick Reference Guide
tags: [cheat-sheet, reference, quick-guide]
---

# Operating Systems Quick Reference

## CPU Scheduling Algorithms

| Algorithm | Type | Advantage | Disadvantage |
|-----------|------|-----------|--------------|
| FCFS | Non-preemptive | Simple, no starvation | Convoy effect |
| SJF | Non-preemptive | Optimal avg waiting time | Needs burst prediction |
| SRTF | Preemptive | Minimal avg waiting time | Overhead, starvation |
| Round Robin | Preemptive | Fair, good for time-sharing | Context switch overhead |
| Priority | Both | Important tasks first | Starvation without aging |

### Formulas

```
Turnaround Time = Completion Time - Arrival Time
Waiting Time = Turnaround Time - Burst Time
Response Time = First Run Time - Arrival Time
Average Waiting Time = Sum(Waiting Times) / Number of Processes
```

## Synchronization

### Semaphore Operations

```c
wait(S) {           // P() or down()
    while (S <= 0)
        ; // busy wait
    S--;
}

signal(S) {         // V() or up()
    S++;
}
```

### Critical Section Requirements

1. Mutual Exclusion: Only one process in CS at a time
2. Progress: Decision cannot be postponed indefinitely
3. Bounded Waiting: Limit on times others enter before a waiting process

### Classic Problems

- Producer-Consumer: Use semaphores (full, empty, mutex)
- Readers-Writers: Multiple readers OR one writer
- Dining Philosophers: Deadlock prevention strategies

## Deadlock

### Four Necessary Conditions (Coffman)

1. Mutual Exclusion: Non-shareable resources
2. Hold and Wait: Hold resources while waiting for more
3. No Preemption: Resources cannot be forcibly taken
4. Circular Wait: Circular chain of waiting processes

### Banker's Algorithm

```
Available[m]      // Available resource instances
Max[n][m]         // Maximum demand
Allocation[n][m]  // Currently allocated
Need[n][m]        // Max - Allocation

Safety check: Find sequence where all processes can finish
```

## Memory Management

### Paging

```
Logical Address = Page Number + Page Offset
Physical Address = Frame Number + Page Offset

Page Size = 2^offset_bits
Number of Pages = Address Space / Page Size
Page Table Size = Number of Pages × Entry Size
```

### TLB

```
EAT = (TLB_hit_rate × TLB_time) + 
      ((1 - TLB_hit_rate) × (TLB_time + Memory_time))
```

## Page Replacement Algorithms

| Algorithm | How it works | Notes |
|-----------|--------------|-------|
| FIFO | Replace oldest page | Belady's anomaly |
| Optimal | Replace page unused longest | Theoretical only |
| LRU | Replace least recently used | Good but expensive |
| Second Chance | FIFO with reference bit | Approximates LRU |

### Belady's Anomaly

More frames can sometimes cause MORE page faults (FIFO only).

## File Systems

### Inode Structure

```
Inode contains:
- File metadata (permissions, owner, timestamps)
- Pointers to data blocks:
  * Direct pointers (12 typically)
  * Single indirect
  * Double indirect
  * Triple indirect
```

### File Allocation Methods

1. Contiguous: Fast sequential, external fragmentation
2. Linked: No fragmentation, slow random access
3. Indexed: Fast access, index overhead

## Important Commands

### Process Management

```bash
ps aux              # All processes
ps -ef              # Full format
top                 # Interactive viewer
kill -9 <PID>       # Force kill
nice -n 10 cmd      # Run with priority
```

### Memory

```bash
free -h             # Memory usage
vmstat              # Virtual memory stats
cat /proc/meminfo   # Detailed info
```

### File System

```bash
df -h               # Disk space
du -sh dir/         # Directory size
ls -li              # List with inodes
stat file           # File metadata
chmod 755 file      # Change permissions
```

## Common Calculations

### Average Waiting Time

```
AWT = Sum(Waiting Time) / Number of Processes
```

### CPU Utilization

```
CPU Utilization = (Time CPU is busy / Total time) × 100%
```

### Throughput

```
Throughput = Number of processes completed / Total time
```

## File Permissions

```
rwxr-xr--
|||
||└- Others: read only
|└-- Group: read + execute
└--- Owner: read + write + execute

Octal: r=4, w=2, x=1
rwx = 7, r-x = 5, r-- = 4
Example: 755 = rwxr-xr-x
```

## Exam Tips

1. Draw diagrams for Gantt charts, state transitions, page tables
2. Show all work for scheduling calculations
3. Check for deadlock using resource allocation graphs
4. Remember formulas for memory addressing
5. Understand tradeoffs between algorithms

## Related Notes

- [[03 CPU Scheduling]]
- [[04 Process Synchronization]]
- [[05 Deadlock]]
- [[06 Memory Management]]
- [[07 Virtual Memory]]

Last Updated: April 2026

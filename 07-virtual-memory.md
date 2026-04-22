---
title: 07 Virtual Memory
tags: [virtual-memory, page-replacement, lru, fifo, thrashing, demand-paging]
course: CIS3110
---

# 07 Virtual Memory

## Demand Paging

Pages are loaded into memory only when they are demanded during program execution.

**Benefits**:
- Less I/O needed
- Less memory needed
- Faster response
- More processes can run

**Valid/Invalid bit**: Indicates whether a page is in memory or on disk.

## Page Fault

Occurs when a process tries to access a page not currently in memory.

**Steps when page fault occurs**:
1. Check page table - page is marked invalid
2. OS traps to page fault handler
3. Find free frame (or evict a page if memory full)
4. Read desired page from disk into frame
5. Update page table
6. Restart the instruction that caused the fault

## Page Replacement Algorithms

When memory is full, must choose which page to evict.

### FIFO (First-In, First-Out)

Replace the oldest page (first one brought into memory).

**Advantage**: Simple to implement  
**Disadvantage**: May replace frequently used pages, suffers from Belady's Anomaly

**Belady's Anomaly**: Increasing the number of frames can sometimes increase page faults (FIFO only).

**Example**:
```
Reference string: 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5
Frames: 3

Step 1: [1, -, -] Fault
Step 2: [1, 2, -] Fault
Step 3: [1, 2, 3] Fault
Step 4: [4, 2, 3] Fault (replace 1)
Step 5: [4, 2, 3] Hit
Step 6: [4, 2, 3] Hit
Step 7: [4, 2, 5] Fault (replace 3)
...
Total: 10 faults
```

### Optimal (OPT)

Replace the page that will not be used for the longest time in the future.

**Advantage**: Minimal page faults (theoretical optimum)  
**Disadvantage**: Requires future knowledge - impossible to implement in practice

**Purpose**: Used as a benchmark to compare other algorithms.

### LRU (Least Recently Used)

Replace the page that has not been used for the longest time in the past.

**Idea**: Use the past as a predictor of the future (locality of reference)

**Advantage**: Good approximation of OPT, performs well in practice  
**Disadvantage**: Expensive to implement

**Implementation options**:
1. **Counter-based**: Every page gets a timestamp, updated on every reference. To evict, scan for smallest timestamp. Expensive - requires search on every fault.

2. **Stack-based**: Keep a stack of page numbers. On every reference, move that page to the top. Bottom of stack is LRU page. No search needed, but updating stack on every access requires hardware support.

**Example**:
```
Reference string: 1, 2, 3, 1, 4, 1, 2, 3, 4, 2
Frames: 3

Step 1: [1, -, -] Fault
Step 2: [1, 2, -] Fault
Step 3: [1, 2, 3] Fault
Step 4: [1, 2, 3] Hit (1 most recent)
Step 5: [4, 2, 3] Fault (replace 1 - least recent)
Step 6: [4, 2, 3] Hit
Step 7: [4, 1, 3] Fault (replace 2)
...
Total: 7 faults
```

### Second Chance (Clock Algorithm)

Approximation of LRU using a reference bit.

**How it works**:
1. Pages arranged in circular queue (like a clock)
2. Reference bit set to 1 when page is accessed (by hardware)
3. Clock hand points to next candidate page
4. If reference bit = 0, evict this page
5. If reference bit = 1, give it a "second chance": set bit to 0, advance hand, check next page
6. If all pages have reference bit = 1, go around the clock clearing all bits, then evict the page where you started

**Advantage**: Simple, reasonably good performance  
**Disadvantage**: Not as good as true LRU

## Frame Allocation

How many frames should each process get?

### Equal Allocation

Each process gets the same number of frames: m/n frames each (where m = total frames, n = number of processes)

**Problem**: Ignores process size differences

### Proportional Allocation

Each process gets frames proportional to its size.

If process i has size s_i and total size S = sum of all s_i, then process i gets:
```
frames_i = (s_i / S) × m
```

## Thrashing

A process is thrashing when it spends more time paging than executing.

**Cause**: Process doesn't have enough frames for its working set

**Working Set**: Set of pages a process is actively using

**Solution**:
- Decrease degree of multiprogramming (run fewer processes)
- Increase available memory
- Use better page replacement algorithm
- Increase process priority to get more frames

## Effective Access Time with Paging

```
Memory access time = 200ns
Page fault service time = 10ms = 10,000,000ns
Page fault rate = p

EAT = (1 - p) × memory_access_time + p × page_fault_service_time
EAT = (1 - p) × 200 + p × 10,000,000

For 10% performance degradation:
EAT must be < 1.1 × 200 = 220ns
220 = 200 + 10,000,000 × p
20 = 10,000,000 × p
p < 0.000002 (one page fault per 500,000 accesses)
```

## Related Topics

- [[06 Memory Management]]
- [[08 File Systems]]
- [[03 CPU Scheduling]]

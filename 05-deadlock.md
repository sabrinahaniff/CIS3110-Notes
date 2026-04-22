---
title: 05 Deadlock
tags: [deadlock, bankers-algorithm, prevention, avoidance, detection]
course: CIS3110
---

# 05 Deadlock

## Definition

A deadlock is a situation where a set of processes are blocked because each process is holding a resource and waiting for another resource held by another process in the set.

## Four Necessary Conditions (Coffman Conditions)

ALL four must hold simultaneously for deadlock to occur:

1. **Mutual Exclusion**: At least one resource must be non-shareable (only one process can use it at a time)

2. **Hold and Wait**: A process holds at least one resource and is waiting to acquire additional resources held by other processes

3. **No Preemption**: Resources cannot be forcibly taken from processes - they must be released voluntarily

4. **Circular Wait**: A circular chain of processes exists, where each process holds a resource needed by the next process in the chain

## Resource Allocation Graph

A directed graph used to detect deadlock:
- **Vertices**: Processes (circles) and Resources (squares)
- **Edges**: 
  - Request edge: Process -> Resource (process requesting resource)
  - Assignment edge: Resource -> Process (resource assigned to process)

**Deadlock detection**: If the graph contains a cycle AND each resource type has only one instance, then deadlock exists.

## Deadlock Handling Strategies

### 1. Prevention

Ensure at least one of the four necessary conditions cannot hold:

**Prevent Mutual Exclusion**: Make resources sharable where possible (not always feasible)

**Prevent Hold and Wait**: Require a process to request all resources at once before it executes

**Prevent No Preemption**: Allow the OS to take resources back from a process if needed

**Prevent Circular Wait**: Impose a fixed ordering of resource types and require requests in that order

### 2. Avoidance

**Banker's Algorithm**: The system decides whether to grant a resource request based on whether it leads to a safe state.

**Safe State**: A state where there exists a sequence of processes that can all complete without deadlock.

**Algorithm**:
```
Data structures:
Available[m]      // Available instances of each resource type
Max[n][m]         // Maximum demand of each process
Allocation[n][m]  // Currently allocated resources
Need[n][m]        // Max - Allocation

Safety Algorithm:
1. Find a process i where Need[i] <= Available
2. If found, assume it finishes and releases resources:
   Available = Available + Allocation[i]
3. Repeat until all processes can finish (safe) or no such process exists (unsafe)

Resource Request Algorithm:
When process Pi requests resources:
1. Check if Request[i] <= Need[i] (not exceeding max)
2. Check if Request[i] <= Available (resources available)
3. Pretend to allocate: run safety algorithm
4. If safe, grant request; if unsafe, make Pi wait
```

### 3. Detection and Recovery

**Detection**: Periodically run an algorithm to check for cycles in the resource allocation graph

**Recovery**:
- **Process termination**: Abort one or more processes to break the cycle
  - Abort all deadlocked processes
  - Abort one at a time until deadlock is eliminated
  
- **Resource preemption**: Take resources from some processes and give to others
  - Select a victim
  - Rollback the victim process
  - Prevent starvation (don't always pick same victim)

### 4. Ignore the Problem (Ostrich Algorithm)

Assume deadlocks are rare and let the user deal with them if they occur. Used by many operating systems (including UNIX/Linux).

## Example Problem

**Given**:
```
Processes: P0, P1, P2
Resources: A, B, C

Allocation:
P0: [0, 1, 0]
P1: [2, 0, 0]
P2: [3, 0, 2]

Max:
P0: [7, 5, 3]
P1: [3, 2, 2]
P2: [9, 0, 2]

Available: [3, 3, 2]
```

**Calculate Need**:
```
Need = Max - Allocation

Need:
P0: [7, 4, 3]
P1: [1, 2, 2]
P2: [6, 0, 0]
```

**Safety check**: Find a safe sequence where each process can complete.

## Related Topics

- [[04 Process Synchronization]]
- [[06 Memory Management]]
- [[03 CPU Scheduling]]

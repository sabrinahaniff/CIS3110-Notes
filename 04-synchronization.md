---
title: 04 Process Synchronization
tags: [synchronization, semaphores, mutex, critical-section, race-conditions]
course: CIS3110
---

# 04 Process Synchronization

## Critical Section Problem

The critical section is a segment of code where a process accesses shared resources (variables, files, etc.).

### Requirements for a Solution

1. **Mutual Exclusion**: Only one process in the critical section at a time
2. **Progress**: Decision on who enters CS cannot be postponed indefinitely  
3. **Bounded Waiting**: Limit on times others can enter CS before a waiting process gets in

## Race Conditions

A race condition occurs when two or more processes access shared data concurrently, and the final result depends on the timing of their execution.

## Synchronization Primitives

### Semaphores

A semaphore S is an integer variable that can only be accessed through two atomic operations:

```c
wait(S) {           // Also called P() or down()
    while (S <= 0)
        ; // busy wait
    S--;
}

signal(S) {         // Also called V() or up()
    S++;
}
```

**Types**:
- **Binary semaphore**: Values 0 or 1 (like a mutex)
- **Counting semaphore**: Can have any non-negative value

### Mutex (Mutual Exclusion)

A mutex is essentially a binary semaphore used specifically for mutual exclusion.

**Usage**:
```c
acquire(mutex);
// critical section
release(mutex);
```

## Classic Synchronization Problems

### Producer-Consumer Problem

**Setup**: Producers create items and put them in a shared buffer. Consumers take items from the buffer.

**Synchronization needed**:
- Mutex to protect the buffer
- Semaphore to count empty slots
- Semaphore to count full slots

**Solution using semaphores**:
```
mutex = 1       // Binary semaphore for mutual exclusion
empty = N       // N empty slots initially
full = 0        // 0 full slots initially

Producer:
    wait(empty)     // Wait for empty slot
    wait(mutex)     // Enter critical section
    // Add item to buffer
    signal(mutex)   // Leave critical section
    signal(full)    // Signal one more full slot

Consumer:
    wait(full)      // Wait for full slot
    wait(mutex)     // Enter critical section
    // Remove item from buffer
    signal(mutex)   // Leave critical section
    signal(empty)   // Signal one more empty slot
```

### Readers-Writers Problem

**Setup**: Multiple processes want to access shared data. Readers only read, writers modify.

**Rules**:
- Multiple readers can read simultaneously
- Only one writer can write at a time
- No readers when a writer is writing

**First readers-writers solution**: Readers have priority (writers may starve)

**Second readers-writers solution**: Writers have priority (readers may starve)

### Dining Philosophers Problem

**Setup**: 5 philosophers sit at a round table with 5 chopsticks (one between each pair). Each philosopher alternates between thinking and eating. To eat, a philosopher needs both adjacent chopsticks.

**Problem**: Deadlock can occur if all philosophers pick up their left chopstick simultaneously.

**Solutions**:
1. Allow at most 4 philosophers to sit at the table
2. Pick up chopsticks only if both are available (atomic operation)
3. Asymmetric solution: odd philosophers pick up left first, even philosophers pick up right first

## Deadlock vs Starvation

**Deadlock**: A set of processes are waiting for each other in a cycle, so none can proceed

**Starvation**: A process waits indefinitely because other processes keep getting priority

## Monitor

A monitor is a high-level synchronization construct that encapsulates:
- Shared data
- Procedures that operate on the data
- Synchronization primitives

Only one process can be active in the monitor at a time (automatic mutual exclusion).

## Related Topics

- [[03 CPU Scheduling]]
- [[05 Deadlock]]
- [[02 Processes and Process Management]]

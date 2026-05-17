# Process Synchronization

## Critical Section Problem

The critical section is a segment of code where a process accesses shared resources.

```mermaid
flowchart TD
    A(["Process enters"]) --> B["Entry Section\n(acquire lock)"]
    B --> C["Critical Section\n(access shared resource)"]
    C --> D["Exit Section\n(release lock)"]
    D --> E["Remainder Section\n(other work)"]
    E --> B

    style C fill:#ef5350,color:#fff
    style B fill:#f57c00,color:#fff
    style D fill:#00897b,color:#fff
```

### Requirements for a Solution

1. **Mutual Exclusion**: Only one process in the critical section at a time
2. **Progress**: Decision on who enters CS cannot be postponed indefinitely
3. **Bounded Waiting**: Limit on times others can enter CS before a waiting process gets in

---

## Race Conditions

A race condition occurs when two or more processes access shared data concurrently, and the final result depends on timing.

---

## Semaphores

A semaphore `S` is an **integer counter** accessed through two atomic operations.

### How a Semaphore Works

```mermaid
flowchart TD
    subgraph wait_op["wait(S) — also called P() or down()"]
        W1(["Process calls wait(S)"]) --> W2{"S > 0?"}
        W2 -->|"Yes"| W3["S-- \ndecrement counter"]
        W3 --> W4(["Process enters\ncritical section"])
        W2 -->|"No"| W5["Process BLOCKS\nadded to wait queue"]
        W5 --> W2
    end

    subgraph signal_op["signal(S) — also called V() or up()"]
        S1(["Process calls signal(S)"]) --> S2["S++\nincrement counter"]
        S2 --> S3{"Any process\nblocked?"}
        S3 -->|"Yes"| S4["Wake up one\nwaiting process"]
        S3 -->|"No"| S5(["Done"])
        S4 --> S5
    end

    style W4 fill:#00897b,color:#fff
    style W5 fill:#ef5350,color:#fff
    style S4 fill:#7c4dff,color:#fff
```

### Semaphore as a Traffic Light

```mermaid
sequenceDiagram
    participant P1 as Process 1
    participant S as Semaphore (S=1)
    participant P2 as Process 2

    P1->>S: wait(S)
    Note over S: S=1 → S-- → S=0
    Note over P1: Enters critical section

    P2->>S: wait(S)
    Note over S: S=0 → BLOCK P2
    Note over P2: Waiting...

    P1->>S: signal(S)
    Note over S: S=0 → S++ → S=1\nWake up P2
    Note over P1: Leaves critical section

    S-->>P2: Wake up!
    Note over P2: Enters critical section
    P2->>S: signal(S)
    Note over S: S=1
    Note over P2: Leaves critical section
```

### Types of Semaphores

**Binary semaphore (mutex):** Values 0 or 1 — used for mutual exclusion

**Counting semaphore:** Any non-negative value — used to manage a pool of resources

---

## Mutex (Mutual Exclusion)

A mutex is a binary semaphore used specifically for mutual exclusion.

```c
acquire(mutex);
// --- critical section ---
release(mutex);
```

---

## Classic Synchronization Problems

### Producer-Consumer Problem

```mermaid
flowchart LR
    P["Producer"] -->|"add item"| B["Shared Buffer\n(size N)"]
    B -->|"remove item"| C["Consumer"]

    subgraph Semaphores
        M["mutex = 1\n(protect buffer)"]
        E["empty = N\n(free slots)"]
        F["full = 0\n(filled slots)"]
    end

    style B fill:#1565c0,color:#fff
    style M fill:#f57c00,color:#fff
    style E fill:#00897b,color:#fff
    style F fill:#7c4dff,color:#fff
```

```mermaid
sequenceDiagram
    participant PR as Producer
    participant SE as empty (N→)
    participant SM as mutex (1)
    participant SF as full (→0)
    participant CO as Consumer

    Note over SE,SF: Initial: empty=N, mutex=1, full=0

    PR->>SE: wait(empty) — empty slot available?
    PR->>SM: wait(mutex) — lock buffer
    Note over PR: Add item to buffer
    PR->>SM: signal(mutex) — unlock buffer
    PR->>SF: signal(full) — notify consumer

    CO->>SF: wait(full) — item available?
    CO->>SM: wait(mutex) — lock buffer
    Note over CO: Remove item from buffer
    CO->>SM: signal(mutex) — unlock buffer
    CO->>SE: signal(empty) — notify producer
```

---

### Readers-Writers Problem

**Rules:**
- Multiple readers can read simultaneously
- Only one writer can write at a time
- No readers when a writer is writing

```mermaid
flowchart LR
    R1["Reader 1"] --> DB[(Shared Data)]
    R2["Reader 2"] --> DB
    R3["Reader 3"] --> DB
    W1["Writer"] -->|"exclusive access"| DB

    style W1 fill:#ef5350,color:#fff
    style DB fill:#1565c0,color:#fff
```

**First readers-writers solution**: Readers have priority (writers may starve)

**Second readers-writers solution**: Writers have priority (readers may starve)

---

### Dining Philosophers Problem

```mermaid
flowchart TD
    subgraph Table
        P0(["Philosopher 0"]) --- C01["Chopstick 0"]
        C01 --- P1(["Philosopher 1"])
        P1 --- C12["Chopstick 1"]
        C12 --- P2(["Philosopher 2"])
        P2 --- C23["Chopstick 2"]
        C23 --- P3(["Philosopher 3"])
        P3 --- C34["Chopstick 3"]
        C34 --- P4(["Philosopher 4"])
        P4 --- C40["Chopstick 4"]
        C40 --- P0
    end
```

**Problem:** Deadlock if all philosophers pick up their left chopstick simultaneously.

**Solutions:**
1. Allow at most 4 philosophers to sit at the table
2. Pick up chopsticks only if both are available (atomic)
3. Asymmetric: odd philosophers pick up left first, even pick up right first

---

## Deadlock vs Starvation

```mermaid
flowchart LR
    subgraph Deadlock
        A["Process A\nwaiting for B"] -->|"holds resource"| B["Process B\nwaiting for A"]
        B -->|"holds resource"| A
    end

    subgraph Starvation
        HP["High-priority\nprocesses"] -->|"always get\nscheduled first"| LP["Low-priority\nprocess waits\nindefinitely"]
    end

    style A fill:#ef5350,color:#fff
    style B fill:#ef5350,color:#fff
    style LP fill:#f57c00,color:#fff
```

**Deadlock**: A set of processes are waiting for each other in a cycle — none can proceed

**Starvation**: A process waits indefinitely because other processes keep getting priority

---

## Monitor

A monitor is a high-level synchronization construct that encapsulates:

- Shared data
- Procedures that operate on the data
- Synchronization primitives (automatic mutual exclusion)

Only one process can be active in the monitor at a time.
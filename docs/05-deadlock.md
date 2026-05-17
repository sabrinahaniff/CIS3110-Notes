# Deadlock

## Definition

A deadlock is a situation where a set of processes are blocked because each process is holding a resource and waiting for another resource held by another process in the set.

---

## Four Necessary Conditions (Coffman Conditions)

ALL four must hold simultaneously for deadlock to occur:

```mermaid
flowchart TD
    D(["DEADLOCK"]) --> C1["1. Mutual Exclusion\nResource can only be\nheld by one process"]
    D --> C2["2. Hold and Wait\nProcess holds resource\nwhile waiting for another"]
    D --> C3["3. No Preemption\nResources can't be\nforcibly taken away"]
    D --> C4["4. Circular Wait\nP1 waits for P2\nP2 waits for P1..."]

    style D fill:#ef5350,color:#fff
    style C1 fill:#f57c00,color:#fff
    style C2 fill:#f57c00,color:#fff
    style C3 fill:#f57c00,color:#fff
    style C4 fill:#f57c00,color:#fff
```

1. **Mutual Exclusion**: At least one resource must be non-shareable
2. **Hold and Wait**: A process holds at least one resource and is waiting for more
3. **No Preemption**: Resources cannot be forcibly taken from processes
4. **Circular Wait**: A circular chain of processes, each waiting for the next

---

## Resource Allocation Graph

```mermaid
flowchart LR
    P1(("P1")) -->|"requests"| R1["R1"]
    R1 -->|"assigned to"| P2(("P2"))
    P2 -->|"requests"| R2["R2"]
    R2 -->|"assigned to"| P1(("P1"))

    style P1 fill:#ef5350,color:#fff
    style P2 fill:#ef5350,color:#fff
    style R1 fill:#1565c0,color:#fff
    style R2 fill:#1565c0,color:#fff
```

**Deadlock detected**: Cycle exists + each resource has only one instance

- **Request edge**: Process → Resource (process requesting)
- **Assignment edge**: Resource → Process (resource assigned)

---

## Deadlock Handling Strategies

### 1. Prevention

Eliminate at least one of the four conditions:

| Condition | Prevention Strategy |
|-----------|-------------------|
| Mutual Exclusion | Make resources sharable (not always possible) |
| Hold and Wait | Request all resources at once before executing |
| No Preemption | Allow OS to take resources back |
| Circular Wait | Impose a fixed ordering on resource requests |

---

### 2. Avoidance: Banker's Algorithm

The system decides whether to grant a resource request based on whether it leads to a **safe state**.

```mermaid
flowchart TD
    A(["Process requests\nresources"]) --> B{"Request <=\nNeed?"}
    B -->|"No"| ERR(["Error: exceeds\nmax claim"])
    B -->|"Yes"| C{"Request <=\nAvailable?"}
    C -->|"No"| WAIT(["Process must wait"])
    C -->|"Yes"| D["Pretend to allocate\nRun safety check"]
    D --> E{"Safe state?"}
    E -->|"Yes"| GRANT(["Grant resources"])
    E -->|"No"| WAIT2(["Process must wait\nRollback allocation"])

    style GRANT fill:#00897b,color:#fff
    style ERR fill:#ef5350,color:#fff
    style WAIT fill:#f57c00,color:#fff
    style WAIT2 fill:#f57c00,color:#fff
```

**Key data structures:**
```
Available[m]      // Available instances of each resource type
Max[n][m]         // Maximum demand of each process
Allocation[n][m]  // Currently allocated resources
Need[n][m]        // Max - Allocation
```

**Safety Algorithm:**
1. Find a process `i` where `Need[i] <= Available`
2. If found, assume it finishes: `Available = Available + Allocation[i]`
3. Repeat until all processes finish (safe) or none found (unsafe)

---

### 3. Detection and Recovery

```mermaid
flowchart LR
    A["Periodically run\ndetection algorithm"] --> B{"Cycle found\nin RAG?"}
    B -->|"No"| A
    B -->|"Yes"| C{"Recovery\nstrategy"}
    C --> D["Process termination\nAbort one or all\ndeadlocked processes"]
    C --> E["Resource preemption\nTake resource from\nvictim process"]

    style B fill:#1565c0,color:#fff
    style D fill:#ef5350,color:#fff
    style E fill:#f57c00,color:#fff
```

### 4. Ignore the Problem (Ostrich Algorithm)

Assume deadlocks are rare and let the user deal with them. Used by many OS including UNIX/Linux.

---

## Example Problem

**Given:**
```
Allocation:        Max:           Available:
P0: [0, 1, 0]     P0: [7, 5, 3]  [3, 3, 2]
P1: [2, 0, 0]     P1: [3, 2, 2]
P2: [3, 0, 2]     P2: [9, 0, 2]
```

**Calculate Need = Max - Allocation:**
```
Need:
P0: [7, 4, 3]
P1: [1, 2, 2]
P2: [6, 0, 0]
```

**Safety check:** Find safe sequence where each process can complete.

- P1: Need [1,2,2] <= Available [3,3,2] ✓ → Available becomes [5,3,2]
- P0: Need [7,4,3] > Available [5,3,2] ✗
- P2: Need [6,0,0] > Available [5,3,2] ✗

Try P2: Need [6,0,0] > [5,3,2] ✗ — no safe sequence found with this order, keep trying...
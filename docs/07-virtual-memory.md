# Virtual Memory

## Demand Paging

Pages are loaded into memory only when they are demanded during execution.

```mermaid
flowchart LR
    A(["Program starts"]) --> B["Only load pages\nthat are needed"]
    B --> C{"Page accessed\nin memory?"}
    C -->|"Yes (valid bit=1)"| D(["Access data normally"])
    C -->|"No (valid bit=0)"| E["Page Fault!\nLoad from disk"]
    E --> D

    style E fill:#f57c00,color:#fff
    style D fill:#00897b,color:#fff
```

**Benefits:**
- Less I/O needed
- Less memory needed
- Faster startup
- More processes can run

---

## Page Fault

```mermaid
flowchart TD
    A(["Process accesses page"]) --> B{"Valid bit\nin page table?"}
    B -->|"1 = in memory"| C(["Access data"])
    B -->|"0 = not in memory"| D["OS trap:\npage fault handler"]
    D --> E{"Free frame\navailable?"}
    E -->|"Yes"| G["Load page\nfrom disk"]
    E -->|"No"| F["Run page replacement\nalgorithm — evict a page"]
    F --> G
    G --> H["Update page table\nvalid bit = 1"]
    H --> I(["Restart instruction"])

    style D fill:#f57c00,color:#fff
    style F fill:#ef5350,color:#fff
    style I fill:#00897b,color:#fff
```

---

## Page Replacement Algorithms

### FIFO (First-In, First-Out)

Replace the **oldest** page in memory.

```mermaid
flowchart LR
    A["Page arrives"] --> Q["Queue\n(oldest → newest)"]
    Q -->|"memory full"| V["Evict oldest page"]
    V --> Q

    style V fill:#ef5350,color:#fff
```

!!! warning "Belady's Anomaly"
    More frames can sometimes cause MORE page faults with FIFO!

---

### Optimal (OPT)

Replace the page that will **not be used for the longest time** in the future.

- **Advantage**: Theoretical minimum page faults
- **Disadvantage**: Requires future knowledge — impossible to implement
- **Purpose**: Benchmark to compare other algorithms

---

### LRU (Least Recently Used)

Replace the page **not used for the longest time in the past**.

```mermaid
flowchart LR
    A(["Page accessed"]) --> B["Move to\nmost-recent position"]
    B --> C["Stack/list\n[most recent ... least recent]"]
    C -->|"memory full"| D["Evict page\nat bottom (LRU)"]

    style D fill:#ef5350,color:#fff
    style C fill:#1565c0,color:#fff
```

- **Advantage**: Good approximation of OPT
- **Disadvantage**: Expensive to implement (needs hardware support)

---

### Second Chance (Clock Algorithm)

Approximation of LRU using a **reference bit**.

```mermaid
flowchart TD
    A(["Need to evict a page"]) --> B{"Reference\nbit = 0?"}
    B -->|"Yes"| C(["Evict this page"])
    B -->|"No"| D["Set bit to 0\nGive second chance"]
    D --> E["Advance clock hand\nto next page"]
    E --> B

    style C fill:#ef5350,color:#fff
    style D fill:#f57c00,color:#fff
```

---

## Page Replacement Comparison

| Algorithm | Faults | Implementable | Notes |
|-----------|--------|---------------|-------|
| OPT | Minimum | No | Theoretical benchmark |
| LRU | Near OPT | Expensive | Best practical choice |
| Second Chance | Good | Yes | LRU approximation |
| FIFO | Worst | Yes | Belady's anomaly |

---

## Frame Allocation

### Equal Allocation
Each process gets `m/n` frames (m = total frames, n = processes)

**Problem**: Ignores process size differences

### Proportional Allocation

```
frames_i = (s_i / S) × m

where s_i = size of process i, S = total size, m = total frames
```

---

## Thrashing

A process is **thrashing** when it spends more time paging than executing.

```mermaid
flowchart LR
    A["Too many processes\nrunning at once"] --> B["Each process has\ntoo few frames"]
    B --> C["Constant page faults"]
    C --> D["CPU busy handling\npage faults, not work"]
    D --> E["CPU utilization drops"]
    E -->|"OS thinks: add more processes!"| A

    style C fill:#ef5350,color:#fff
    style D fill:#ef5350,color:#fff
```

**Solution:**
- Decrease degree of multiprogramming (run fewer processes)
- Increase available memory
- Use working set model

---

## Effective Access Time with Paging

```
Memory access time     = 200ns
Page fault service     = 10ms = 10,000,000ns
Page fault rate        = p

EAT = (1-p) × 200 + p × 10,000,000

For max 10% performance degradation:
EAT < 220ns  →  p < 0.000002
(one page fault per 500,000 accesses)
```
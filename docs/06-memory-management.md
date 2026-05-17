# Memory Management

## Address Binding

```mermaid
flowchart LR
    A["Source Code\n(symbolic addresses)"] -->|"compiler"| B["Object File\n(relocatable addresses)"]
    B -->|"linker/loader"| C["Executable\n(logical addresses)"]
    C -->|"MMU at runtime"| D["Physical Memory\n(physical addresses)"]

    style D fill:#00897b,color:#fff
```

The process of mapping logical addresses to physical addresses can happen at:

1. **Compile time**: Absolute code, addresses fixed at compile time
2. **Load time**: Relocatable code, addresses fixed when loaded
3. **Execution time**: Addresses can change during execution (requires MMU hardware)

---

## Base and Limit Registers

```mermaid
flowchart LR
    CPU["CPU generates\nlogical address"] --> CMP{"base <= addr\n< base+limit?"}
    CMP -->|"Yes"| TRANS["Physical addr =\nlogical + base"]
    CMP -->|"No"| TRAP["Trap to OS\n(segfault)"]
    TRANS --> MEM["Access Memory"]

    style TRAP fill:#ef5350,color:#fff
    style MEM fill:#00897b,color:#fff
```

- **Base register**: Smallest legal physical address
- **Limit register**: Size of the address space
- **Translation**: `Physical address = Logical address + Base`

---

## Paging

Divides physical memory into fixed-size **frames** and logical memory into **pages** of the same size.

```mermaid
flowchart LR
    subgraph Logical Address
        PN["Page Number (p)"] 
        PO["Page Offset (d)"]
    end

    subgraph Page Table
        PT["p → frame f"]
    end

    subgraph Physical Address
        FN["Frame Number (f)"]
        FO["Page Offset (d)"]
    end

    PN -->|"lookup"| PT
    PT --> FN
    PO --> FO

    style PT fill:#1565c0,color:#fff
```

### Benefits

- Eliminates external fragmentation
- Allows non-contiguous allocation
- Simplifies memory management

### Page Table Entry (PTE) contains:

- Frame number
- Valid/invalid bit
- Protection bits (read/write/execute)
- Reference bit
- Modify/dirty bit

### Formulas

```
Page size              = 2^(offset_bits)
Number of pages        = Logical address space / Page size
Page table size        = Number of pages × Entry size
Physical address       = (frame number × page size) + page offset
```

### Example

```
Logical address space: 2^16 = 64K
Page size: 2^10 = 1K
Page offset bits: 10
Page number bits: 16 - 10 = 6
Number of pages: 64

Logical address 2500:
  Page number: 2500 / 1024 = 2
  Page offset: 2500 mod 1024 = 452

If page table[2] = frame 6:
  Physical address = (1024 × 6) + 452 = 6596
```

---

## Translation Lookaside Buffer (TLB)

A small, fast cache for page table entries.

```mermaid
flowchart TD
    CPU(["CPU generates\nlogical address"]) --> TLB{"Check TLB"}
    TLB -->|"TLB Hit"| FAST["Get frame number\ndirectly"]
    TLB -->|"TLB Miss"| PT["Access page table\nin memory"]
    PT --> UPD["Update TLB"]
    UPD --> FAST
    FAST --> MEM(["Access physical memory"])

    style TLB fill:#1565c0,color:#fff
    style FAST fill:#00897b,color:#fff
    style PT fill:#f57c00,color:#fff
```

**Effective Access Time:**
```
EAT = (hit_rate × TLB_time) + ((1 - hit_rate) × (TLB_time + mem_time))

Example:
  TLB hit rate = 80%, TLB = 20ns, Memory = 100ns
  EAT = 0.8×20 + 0.2×(20+100) = 16 + 24 = 40ns
```

---

## Multi-level Paging

For large address spaces, page tables become too large. Use hierarchical page tables.

```mermaid
flowchart LR
    LA["Logical Address\n| Outer | Inner | Offset |"] --> OPT["Outer Page Table"]
    OPT --> IPT["Inner Page Table"]
    IPT --> PA["Physical Address"]

    style OPT fill:#1565c0,color:#fff
    style IPT fill:#7c4dff,color:#fff
    style PA fill:#00897b,color:#fff
```

---

## Segmentation

Divides memory into variable-size segments based on logical units (code, data, stack).

```mermaid
flowchart LR
    LA["Logical Address\n| Segment | Offset |"] --> ST["Segment Table\n(base, limit)"]
    ST --> CHK{"offset < limit?"}
    CHK -->|"Yes"| PA["Physical =\nbase + offset"]
    CHK -->|"No"| TRAP["Segfault!"]

    style PA fill:#00897b,color:#fff
    style TRAP fill:#ef5350,color:#fff
```

**Segment table** contains:
- **Base**: Starting physical address of segment
- **Limit**: Length of segment

---

## Segmentation with Paging

Combines benefits of both:
- Segments provide logical organization
- Pages eliminate external fragmentation

Each segment has its own page table.
# Memory Management

## Address Binding

The process of mapping logical addresses to physical addresses can happen at different times:

1. **Compile time**: Absolute code, addresses fixed at compile time
2. **Load time**: Relocatable code, addresses fixed when loaded
3. **Execution time**: Addresses can change during execution (requires hardware support - MMU)

## Base and Limit Registers

**Base register**: Holds the smallest legal physical address for a process  
**Limit register**: Holds the size of the address space

**Protection**: For every memory access, hardware checks:
```
Is address >= base AND address < base + limit?
If yes, allow access
If no, trap to OS (segmentation fault)
```

**Address translation**:
```
Physical address = Logical address + Base register value
```

## Paging

Divides physical memory into fixed-size blocks called **frames** and logical memory into blocks of the same size called **pages**.

### Benefits

- Eliminates external fragmentation
- Allows non-contiguous allocation
- Simplifies memory management

### Page Table

Maps logical page numbers to physical frame numbers.

**Structure**:
```
Page Table Entry (PTE) contains:
- Frame number
- Valid/invalid bit
- Protection bits (read/write/execute)
- Reference bit (used for page replacement)
- Modify/dirty bit (page has been written to)
```

### Address Translation

**Logical address** divided into:
- **Page number** (p): Index into page table
- **Page offset** (d): Offset within the page

**Physical address**:
- **Frame number** (from page table[p])
- **Page offset** (same as logical address offset)

**Formulas**:
```
Page size = 2^(offset_bits)
Number of pages = Logical address space / Page size
Page table size = Number of pages × Entry size

Logical address: | Page number (p) | Page offset (d) |
Physical address: | Frame number (f) | Page offset (d) |
```

### Example

```
Logical address space: 2^16 = 64K
Page size: 2^10 = 1K
Page offset bits: 10
Page number bits: 16 - 10 = 6
Number of pages: 2^6 = 64 pages

Logical address 2500:
Page number: 2500 / 1024 = 2
Page offset: 2500 mod 1024 = 452

If page table[2] = frame 6:
Physical address = (1024 × 6) + 452 = 6596
```

## Translation Lookaside Buffer (TLB)

A small, fast cache for page table entries.

**How it works**:
1. CPU generates logical address
2. Check TLB for page number
3. If TLB hit: Get frame number immediately
4. If TLB miss: Access page table in memory, update TLB

**Effective Access Time**:
```
EAT = (TLB hit rate × TLB access time) + 
      ((1 - TLB hit rate) × (TLB access time + Memory access time))
```

**Example**:
```
TLB hit rate = 80%
TLB access = 20ns
Memory access = 100ns

EAT = 0.8 × 20 + 0.2 × (20 + 100)
    = 16 + 0.2 × 120
    = 16 + 24
    = 40ns
```

## Multi-level Paging

For large address spaces, page tables become too large. Solution: hierarchical page tables.

**Two-level paging**:
```
Logical address: | Outer page | Inner page | Offset |

1. Use outer page number to index outer page table
2. Get address of inner page table
3. Use inner page number to index inner page table
4. Get frame number
5. Combine frame number + offset
```

## Segmentation

Divides memory into variable-size segments based on logical units (code, data, stack).

**Segment table** contains:
- Base: Starting physical address of segment
- Limit: Length of segment

**Address translation**:
```
Logical address: | Segment number | Offset |

1. Check if offset < limit (protection)
2. Physical address = base[segment] + offset
```

## Segmentation with Paging

Combines benefits of both:
- Segments provide logical organization
- Pages eliminate external fragmentation

Each segment has its own page table.

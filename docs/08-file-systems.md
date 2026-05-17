# File Systems

## File Concept

A file is a named collection of related information stored on secondary storage.

**Attributes:** Name, Type, Location, Size, Protection, Timestamps, Owner

**Operations:** Create, Write, Read, Seek, Delete, Truncate

---

## Directory Structure

```mermaid
flowchart TD
    R["/\n(root)"] --> H["/home"]
    R --> E["/etc"]
    R --> U["/usr"]
    H --> S["/home/sabrina"]
    H --> B["/home/bob"]
    S --> D["docs/"]
    S --> F["notes.txt"]
    D --> F2["report.pdf"]

    style R fill:#7c4dff,color:#fff
    style S fill:#1565c0,color:#fff
```

- **Absolute path**: `/home/sabrina/notes.txt`
- **Relative path**: `../bob/file.txt`

---

## File Allocation Methods

### 1. Contiguous Allocation

```mermaid
flowchart LR
    F["File\n(start=2, length=4)"] --> B2["Block 2"]
    B2 --> B3["Block 3"]
    B3 --> B4["Block 4"]
    B4 --> B5["Block 5"]

    style F fill:#1565c0,color:#fff
    style B2 fill:#00897b,color:#fff
    style B3 fill:#00897b,color:#fff
    style B4 fill:#00897b,color:#fff
    style B5 fill:#00897b,color:#fff
```

- Fast sequential and random access
- External fragmentation
- Hard to grow files

### 2. Linked Allocation

```mermaid
flowchart LR
    F["File\n(start=2)"] --> B2["Block 2\ndata | next→5"]
    B2 --> B5["Block 5\ndata | next→8"]
    B5 --> B8["Block 8\ndata | next→NULL"]

    style F fill:#1565c0,color:#fff
```

- No external fragmentation
- Slow random access (must traverse list)
- Pointer overhead

### 3. Indexed Allocation

```mermaid
flowchart LR
    F["File\n(index block=3)"] --> IB["Index Block 3\n[5, 8, 12, 17, ...]"]
    IB --> B5["Block 5\ndata"]
    IB --> B8["Block 8\ndata"]
    IB --> B12["Block 12\ndata"]

    style IB fill:#f57c00,color:#fff
    style F fill:#1565c0,color:#fff
```

- Fast random access
- No external fragmentation
- Overhead of index block

---

## Inodes (Unix/Linux)

```mermaid
flowchart TD
    I["Inode\n(metadata + pointers)"] --> DP["12 Direct Pointers\n→ data blocks directly\n(48KB with 4KB blocks)"]
    I --> SP["Single Indirect\n→ block of pointers\n(4MB)"]
    I --> DP2["Double Indirect\n→ block of single indirect\n(4GB)"]
    I --> TP["Triple Indirect\n→ block of double indirect\n(4TB)"]

    DP --> DB1["Data Block"]
    SP --> PB["Pointer Block"] --> DB2["Data Block"]

    style I fill:#7c4dff,color:#fff
    style DP fill:#00897b,color:#fff
    style SP fill:#1565c0,color:#fff
    style DP2 fill:#f57c00,color:#fff
    style TP fill:#ef5350,color:#fff
```

**Example with 4KB blocks:**
```
12 direct:        12 × 4KB       =  48KB
Single indirect:  1K × 4KB       =   4MB
Double indirect:  1K × 1K × 4KB  =   4GB
Triple indirect:  1K³ × 4KB      =   4TB
```

---

## Free Space Management

| Method | How | Pros | Cons |
|--------|-----|------|------|
| Bitmap | 1 bit per block (0=free, 1=used) | Simple, find contiguous blocks | Extra space |
| Linked list | Free blocks linked together | No waste | Slow traversal |
| Grouping | Addresses of n free blocks stored in first free block | Efficient | Complex |
| Counting | First free block + count of contiguous free blocks | Good for contiguous | More complex |

---

## File Permissions (Unix/Linux)

```
rwxr-xr--
|||
||└─ Others:  r-- = 4
|└── Group:   r-x = 5
└─── Owner:   rwx = 7

Octal: 754
```

**For files**: `x` means executable  
**For directories**: `x` means can enter/traverse

---

## Useful Commands

```bash
# List files with inodes
ls -li

# Display file metadata
stat filename

# Disk space usage
df -h              # File system usage
du -sh directory/  # Directory size

# File permissions
chmod 755 file     # rwxr-xr-x
chown user file    # Change owner
```

---

## DMA (Direct Memory Access)

Device controller transfers data directly between device buffer and main memory — without the CPU copying each byte.

```mermaid
flowchart LR
    CPU(["CPU\ninitializes transfer"]) --> DC["Device Controller"]
    DC -->|"Direct transfer\n(no CPU involved)"| MEM["Main Memory"]
    DC -->|"Interrupt when\ncomplete"| CPU

    style CPU fill:#7c4dff,color:#fff
    style MEM fill:#00897b,color:#fff
```
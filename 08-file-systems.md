# File Systems

## File Concept

A file is a named collection of related information stored on secondary storage.

**Attributes**:
- Name
- Type
- Location
- Size
- Protection (permissions)
- Time, date, and user identification

## File Operations

- Create
- Write
- Read
- Reposition within file (seek)
- Delete
- Truncate

## File Types

- Regular files (text or binary)
- Directories
- Special files (devices)

## Directory Structure

### Single-Level Directory

All files in one directory. Simple but no organization for multiple users.

### Two-Level Directory

Separate directory for each user. Solves name collision problem.

### Tree-Structured Directory

Most common. Hierarchical structure with arbitrary depth.

**Path names**:
- Absolute path: /home/user/file.txt
- Relative path: ../other/file.txt

## File Allocation Methods

### 1. Contiguous Allocation

Each file occupies a set of contiguous blocks on disk.

**Advantages**:
- Simple - only starting location and length needed
- Fast sequential access
- Fast random access

**Disadvantages**:
- External fragmentation
- Difficulty in growing files

### 2. Linked Allocation

Each file is a linked list of disk blocks scattered anywhere on disk.

**Advantages**:
- No external fragmentation
- Files can grow easily

**Disadvantages**:
- Slow random access (must traverse list)
- Space overhead for pointers
- Reliability (if one link breaks, file is lost)

### 3. Indexed Allocation

Brings all pointers together into an index block.

**Advantages**:
- Fast random access
- No external fragmentation

**Disadvantages**:
- Overhead of index block
- What if file is larger than index block?

**Solution for large files**: Multi-level indexing (similar to multi-level page tables)

## Inodes (Unix/Linux)

An inode (index node) contains file metadata and pointers to data blocks.

**Inode structure**:
- File metadata (permissions, owner, timestamps, size)
- Direct pointers (typically 12) - point directly to data blocks
- Single indirect pointer - points to a block of pointers
- Double indirect pointer - points to a block of single indirect pointers
- Triple indirect pointer - points to a block of double indirect pointers

**Maximum file size**: Determined by number of pointers and block size.

**Example with 4KB blocks**:
```
12 direct pointers: 12 × 4KB = 48KB
1 single indirect: 1K pointers × 4KB = 4MB
1 double indirect: 1K × 1K pointers × 4KB = 4GB
1 triple indirect: 1K × 1K × 1K pointers × 4KB = 4TB
```

## Free Space Management

### Bit Vector (Bitmap)

Each block represented by 1 bit. 0 = free, 1 = allocated (or vice versa).

**Advantage**: Simple, easy to find contiguous blocks  
**Disadvantage**: Requires extra space

### Linked List

Free blocks linked together.

**Advantage**: No waste of space  
**Disadvantage**: Slow to traverse

### Grouping

Store addresses of n free blocks in first free block. Last address points to another block containing addresses of free blocks.

### Counting

Store address of first free block and count of contiguous free blocks.

## File System Commands

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

## File Permissions (Unix/Linux)

```
rwxr-xr--
|||
||└─ Others: read only
|└── Group: read + execute
└─── Owner: read + write + execute

Octal notation:
r = 4, w = 2, x = 1
rwx = 7, r-x = 5, r-- = 4
Example: 754 = rwxr-xr--
```

**For files**: x means executable
**For directories**: x means can enter/traverse the directory

## DMA (Direct Memory Access)

Device controller transfers data directly between device buffer and main memory without CPU copying each byte.

**How CPU knows operation is complete**: Device controller generates an interrupt when block transfer completes.

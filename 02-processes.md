---
title: 02 Processes and Process Management
tags: [processes, pcb, fork, exec, ipc, context-switching]
course: CIS3110
---

# 02 Processes and Process Management

## Process Concept

A process is a program in execution. It includes:
- Program code (text section)
- Current activity (program counter, register contents)
- Stack (temporary data like function parameters, return addresses)
- Data section (global variables)
- Heap (dynamically allocated memory)

## Process Control Block (PCB)

The OS maintains a PCB for each process containing:
- Process state (running, waiting, etc.)
- Program counter
- CPU registers
- CPU scheduling information (priority, scheduling queue pointers)
- Memory management information (page tables, segment tables)
- Accounting information (CPU time used, time limits)
- I/O status information (list of open files, I/O devices allocated)

## Process States

A process moves through various states during its lifetime:

1. **New**: Process is being created
2. **Ready**: Process is waiting to be assigned to a processor
3. **Running**: Instructions are being executed
4. **Waiting**: Process is waiting for some event (I/O completion, signal)
5. **Terminated**: Process has finished execution

## Process Creation

### fork() System Call

Creates a new process by duplicating the calling process.

```c
pid_t pid = fork();

if (pid == 0) {
    // Child process code
    printf("I am the child\n");
} else if (pid > 0) {
    // Parent process code
    printf("I am the parent, child PID is %d\n", pid);
} else {
    // fork() failed
    perror("fork");
}
```

**After fork()**:
- Child gets a copy of parent's memory space
- Child has its own PID
- Both processes continue from the point after fork()
- Return value distinguishes parent (positive PID) from child (0)

### exec() Family

Replaces the current process image with a new program.

```c
execlp("ls", "ls", "-l", NULL);
// If successful, this line never executes
perror("execlp");
```

**Common exec() variants**:
- `execl()`: Takes arguments as list
- `execlp()`: Searches PATH for the program
- `execv()`: Takes arguments as array
- `execvp()`: Array + PATH search

### wait() System Call

Parent process waits for child to terminate.

```c
int status;
pid_t child_pid = wait(&status);
printf("Child %d terminated\n", child_pid);
```

## Inter-Process Communication (IPC)

Processes need to communicate and synchronize. Two fundamental models:

### 1. Shared Memory

Processes share a region of memory. Faster but requires synchronization.

### 2. Message Passing

Processes communicate by sending messages. Slower but easier to implement.

**Direct Communication**: Processes explicitly name each other
```
send(P, message)    // Send to process P
receive(Q, message) // Receive from process Q
```

**Indirect Communication**: Messages sent to/received from mailboxes (ports)
```
send(A, message)    // Send to mailbox A
receive(A, message) // Receive from mailbox A
```

## Pipes

### Ordinary Pipes

Unidirectional communication between parent and child processes.

```c
int fd[2];
pipe(fd);    // fd[0] is read end, fd[1] is write end

if (fork() == 0) {
    // Child: write to pipe
    close(fd[0]);
    write(fd[1], "Hello", 5);
    close(fd[1]);
} else {
    // Parent: read from pipe
    close(fd[1]);
    char buf[10];
    read(fd[0], buf, 5);
    close(fd[0]);
}
```

### Named Pipes (FIFOs)

Bidirectional, can be used by unrelated processes, exists as a file in the filesystem.

```bash
mkfifo mypipe
echo "Hello" > mypipe &    # Writer
cat < mypipe               # Reader
```

## Zombie Processes

A terminated process whose parent hasn't yet called wait() to read its exit status.

**Problem**: Takes up space in the process table

**Solution**: Parent should call wait() to reap zombie children

## Orphan Processes

A process whose parent has terminated. The init process (PID 1) adopts orphans.

## Related Topics

- [[01 Introduction to Operating Systems]]
- [[03 CPU Scheduling]]
- [[04 Process Synchronization]]

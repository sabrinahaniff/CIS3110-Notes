# CPU Scheduling

## Overview

When multiple processes are ready, the OS must choose who gets the CPU next - this is CPU scheduling.

### Types

**Non-preemptive**: Once a process starts running, it keeps the CPU until it blocks or finishes

**Preemptive**: The OS can interrupt it (timer interrupt) and run someone else

### Quality Metrics

- Waiting time
- Turnaround time  
- Response time

## Scheduling Algorithms

### FCFS (First-Come, First-Served)

**Rule**: Run processes in the order they arrive in the ready queue

**Type**: Non-preemptive

**Downside**: Can cause the convoy effect - one long CPU-bound job makes lots of short jobs wait.

### SJF (Shortest Job First)

**Rule**: Always run the process with the smallest CPU burst time

**Type**: Non-preemptive

**Upside**: Optimal average waiting time (in theory)

**Downside**: Needs burst-time estimates, can cause starvation for long jobs

### SRTF (Shortest Remaining Time First)

**Description**: Preemptive version of SJF

**Rule**: Always run the process with the smallest remaining CPU burst time

**Preemption**: If a new process arrives with a shorter remaining time than the current running one, the OS preempts (switches) to it

**Upside**: Often minimizes average waiting time (in theory)

**Downside**: Needs burst-time estimates, can cause starvation for long jobs

### Round Robin (RR)

Round Robin is a preemptive CPU scheduling algorithm designed for time-sharing systems.

**How it works**:
- The ready queue is treated like a circular line
- Each process gets a fixed time slice called a time quantum (e.g., 10 ms)
- If a process finishes before the quantum, it leaves the CPU early
- If it doesn't finish, it is preempted at the end of the quantum and put at the back of the ready queue

**Goals**: Fairness and good response time (especially for interactive programs)

**Tradeoff**:
- If quantum is too small: too many context switches
- If quantum is too large: behaves like FCFS (bad response time)

### Priority Scheduling

**Rule**: Each process is assigned a priority number. The CPU goes to the highest-priority ready process.

**Types**:
- Preemptive: If a higher-priority process arrives, preempt the current one
- Non-preemptive: Let the current process finish its burst

**Problem**: Starvation - low-priority processes may never run

**Solution**: Aging - gradually increase the priority of waiting processes

## Formulas

```
Turnaround Time = Completion Time - Arrival Time
Waiting Time = Turnaround Time - Burst Time
Response Time = First Run Time - Arrival Time

Average Waiting Time = Sum(Waiting Times) / Number of Processes
```

## Useful Commands

```bash
# Process status snapshot
ps -p <PID> -o pid,ppid,stat,comm

# Live updating view
top -p <PID>
```

**ps output**:
- pid: process id
- ppid: parent process id (who created it)
- stat: status code (R=running, S=sleeping, T=stopped)
- comm: command/program name

## Scheduling Comparison

| Algorithm | Preemptive | Optimal | Starvation Risk | Overhead |
|-----------|-----------|---------|-----------------|----------|
| FCFS | No | No | No | Low |
| SJF | No | Yes (avg wait) | Yes | Low |
| SRTF | Yes | Yes (avg wait) | Yes | Medium |
| RR | Yes | No | No | High (context switches) |
| Priority | Both | Depends | Yes | Medium |

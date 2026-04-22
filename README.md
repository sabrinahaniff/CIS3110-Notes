---
title: CIS3110 Operating Systems Notes
tags: [index, operating-systems, cis3110]
---

# CIS3110 Operating Systems Notes

Comprehensive notes covering fundamental concepts of operating systems including processes, scheduling, synchronization, memory management, and file systems.

## Course Information

- Course: CIS3110 - Operating Systems
- Format: Obsidian Markdown Notes
- Topics: Complete OS fundamentals from introduction to advanced concepts

## Table of Contents

### Core Topics

1. [[01 Introduction to Operating Systems]]
   - What is an Operating System?
   - System Calls and Kernel Mode
   - Process States

2. [[02 Processes and Process Management]]
   - Process Concept and PCB
   - Process States and Context Switching
   - Process Creation (fork, exec)
   - Inter-Process Communication (IPC)

3. [[03 CPU Scheduling]]
   - Scheduling Algorithms
   - FCFS, SJF, SRTF
   - Round Robin
   - Priority Scheduling

4. [[04 Process Synchronization]]
   - Critical Section Problem
   - Semaphores and Mutex
   - Producer-Consumer Problem
   - Readers-Writers Problem
   - Dining Philosophers Problem

5. [[05 Deadlock]]
   - Deadlock Conditions (Coffman Conditions)
   - Prevention and Avoidance
   - Banker's Algorithm
   - Detection and Recovery

6. [[06 Memory Management]]
   - Memory Allocation Strategies
   - Paging and Segmentation
   - Page Tables and TLB
   - Multi-level Paging

7. [[07 Virtual Memory]]
   - Demand Paging
   - Page Replacement (FIFO, LRU, Optimal)
   - Thrashing
   - Frame Allocation

8. [[08 File Systems]]
   - File Concepts and Operations
   - Directory Structure
   - File Allocation Methods
   - Inodes

## Quick Reference

### Key Scheduling Algorithms

- FCFS: Simple, non-preemptive, convoy effect
- SJF: Optimal average waiting time, requires burst prediction
- SRTF: Preemptive SJF, minimal average waiting time
- Round Robin: Time quantum-based, fair
- Priority: Can cause starvation, needs aging

### Synchronization Primitives

- Semaphore: Integer variable for synchronization
- Mutex: Binary semaphore for mutual exclusion
- Monitor: High-level synchronization construct

### Page Replacement Algorithms

- FIFO: Simple but has Belady's anomaly
- LRU: Good performance, expensive to implement
- Optimal: Best performance, theoretical only

## How to Use These Notes

These notes are formatted for Obsidian, a knowledge base that works on Markdown files.

### Viewing in Obsidian

1. Download or clone this repository
2. Open Obsidian
3. Click "Open folder as vault"
4. Select the os-notes-obsidian folder

### Links Between Notes

- Use [[Note Name]] syntax to link between topics
- Follow links to explore related concepts
- Use graph view to visualize connections

### Tags

All notes are tagged with relevant topics:
- scheduling
- synchronization
- memory
- deadlock
- processes

## Study Tips

1. Follow the order - topics build on each other
2. Work through examples in each section
3. Try the Linux commands mentioned
4. Draw diagrams for processes and memory layouts
5. Practice calculations for scheduling and memory

## Note Format

Each note includes:
- Front matter with metadata
- Main content with explanations
- Code examples and commands
- Formulas and calculations

## License

These are personal study notes for educational purposes.

Last Updated: April 2026  
Course: CIS3110 Operating Systems

## Introduction to Operating Systems
### Pages of introductions answer one foundational question:
#### What is an operating system, and why does it exist at all?

### What happens when a program runs
- A program is a sequence of instructions executed by the CPU
- The CPU repeatedly fetches, decodes, and executes instructions
- This execution model is known as the Von Neumann model
- By itself, this model describes a single program running on hardware

### Why an operating system is needed
- Real systems must run many programs at the same time
- Programs need to share CPU, memory, and devices
- Hardware alone does not provide ease of use or safe sharing
- The operating system exists to manage this complexity

### The core problem: virtualization
- The central problem of operating systems is how to virtualize resources
- Virtualization makes physical resources appear dedicated to each program
- The OS focuses on how to achieve virtualization efficiently and correctly
- Two key aspects of OS design:
  - Mechanisms: how something is done
  - Policies: which decision is made

### What an operating system is
- The operating system is software that manages system resources
- It ensures the system is correct, efficient, and easy to use
- The OS provides virtual versions of physical resources:
  - CPU
  - Memory
  - Disk
- Because of this, the OS can be viewed as a virtual machine

### Interfaces and system calls
- Programs cannot directly access hardware
- The OS provides controlled interfaces called system calls
- System calls allow programs to:
  - run code
  - allocate memory
  - access files and devices
- Standard libraries often wrap system calls for applications

### The OS as a resource manager
- CPU, memory, and disk are shared resources
- Many programs may use these resources at the same time
- The OS manages resource sharing to be:
  - efficient
  - fair
  - safe
- Resource management decisions are central to OS design

### Code example (conceptual)
- Example programs may run indefinitely
- The OS allows multiple such programs to run concurrently
- The OS creates the illusion that each program runs on its own

---
---

## Virtualizing the CPU

### This section answers one question:

#### How can many programs run “at the same time” on a machine with only ONE CPU?

What you are seeing here is the first concrete example of virtualization.

- A machine may have only one physical CPU
- Multiple programs still appear to run at the same time
- This behavior is an illusion created by the operating system

### What really happens
- At any instant, only one program executes on the CPU
- The OS rapidly switches the CPU between programs
- Each program runs for a short time slice

### What users observe
- Program outputs appear interleaved
- Programs seem to run simultaneously
- The system appears responsive

### Definition
- **CPU virtualization** transforms one physical CPU into many virtual CPUs
- Each program behaves as if it has its own processor
- This technique is known as **time sharing**

### Concurrency vs parallelism
- **Concurrency**: multiple programs make progress over time
- **Parallelism**: multiple programs run at the same time on different CPUs
- CPU virtualization provides concurrency, not true parallelism

### Role of the operating system
- The OS controls execution of programs
- It starts, stops, and switches between them
- Programs interact with the OS through **system calls**

### Mechanisms and policies
- **Mechanisms** enable CPU sharing (e.g., context switching)
- **Policies** decide which program runs and for how long
- These decisions define **CPU scheduling**

### OS as a resource manager
- The CPU is a shared resource
- The OS allocates CPU time among programs
- Goals include efficiency, fairness, and responsiveness

---
---

### This section answers:
#### How can multiple programs each believe they have their own memory, even though physical memory is shared?
This is the memory counterpart of CPU virtualization.

## Virtualizing Memory
- Physical memory can be viewed as an array of bytes
- Programs access memory by reading from and writing to addresses
- Memory is accessed constantly when a program runs:
  - for data
  - for instruction fetches

### Single program memory usage
- A program can dynamically allocate memory (e.g., via malloc)
- The program accesses memory through addresses
- The same address is reused as the program runs

### Running multiple programs
- Multiple instances of the same program can run at the same time
- Each program prints the same memory address
- Each program updates its memory independently
- Programs do not interfere with each other

### The illusion of private memory
- Each program behaves as if it has its own private memory
- This is an illusion created by the operating system
- Physical memory is actually shared among programs

### Definition
- **Memory virtualization** provides each process with a private **virtual address space**
- A virtual address space is sometimes called an **address space**
- Memory accesses in one process do not affect other processes or the OS

### Virtual vs physical addresses
- **Virtual address**: address seen and used by a program
- **Physical address**: actual location in physical memory
- The OS maps virtual addresses to physical memory

### Role of the operating system
- The OS manages physical memory as a shared resource
- It ensures isolation and protection between processes
- It provides the illusion that each process has its own memory

---
---

## Concurrency

### This section answers a very important question:
#### What goes wrong when multiple things execute at the same time?

- **Concurrency** refers to multiple activities making progress at the same time
- This can happen even on a single CPU
- Concurrency first appeared inside operating systems
- Modern programs also face concurrency problems

### Threads and shared memory
- A **thread** is an execution unit within a process
- Multiple threads can exist in the same process
- Threads share the same memory space
- Shared memory is the main source of concurrency problems

### Expected behavior
- Two threads increment a shared counter
- Each thread runs the same number of loops
- The expected final value is the sum of both increments

### Actual behavior
- The final value can be incorrect
- Results may differ across runs
- Behavior becomes non-deterministic for larger workloads

### Why things go wrong
- Incrementing a variable is not a single operation
- It involves multiple steps:
  - load value from memory
  - modify the value
  - store it back to memory
- These steps are not **atomic**
- Threads can interleave during these steps

### Core concurrency problem
- Shared data can be accessed at the same time
- Execution order is unpredictable
- Updates can be lost

### Key concepts
- **Concurrency**: multiple execution flows making progress
- **Thread**: execution unit within a process
- **Shared memory**: memory accessible by multiple threads
- **Atomicity**: operation that happens all at once or not at all

### Why this matters
- Concurrency bugs break program correctness
- Bugs can be rare and hard to reproduce
- Correct concurrent programming requires OS and hardware support

---
---

## Persistence

### This section answers a very important question:
#### How can data survive after a program finishes or the system crashes?

- **Persistence** refers to data that outlives a running program
- Main memory (e.g., DRAM) is **volatile**
- When power is lost or the system crashes, memory contents disappear
- Persistent storage is required to preserve data long-term

### Why persistence is needed
- Programs store data in memory while running
- Memory is cleared when a program ends or the system shuts down
- Users expect data to remain available across reboots and crashes

### Hardware support for persistence
- Persistence is provided by **I/O devices**
- Common persistent storage devices include:
  - hard drives
  - solid-state drives (SSDs)
- These devices retain data even without power

### Software support: the file system
- The **file system** is the OS component that manages persistent data
- It stores user data as **files**
- It is responsible for:
  - correctness
  - efficiency
  - reliability

### Sharing persistent data
- Unlike CPU and memory, disk storage is not private per process
- Files are intended to be shared across programs
- Files persist across:
  - program executions
  - different processes
  - time

### Accessing persistent storage
- Programs do not access disks directly
- They use **system calls** such as:
  - open
  - write
  - close
- The OS routes these requests to the file system

### Reliability challenges
- Disk operations are slow
- Crashes can occur during writes
- File systems must handle partial failures

### Why this matters
- Persistence is essential for user data
- File systems must balance:
  - performance
  - correctness
  - recovery after failures


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

---
---

## Design Goals

### This section answers a very important question:
#### What should an operating system be good at?

- An operating system manages physical resources such as CPU, memory, and disk
- It handles concurrency and provides persistent storage
- OS design requires making trade-offs between competing goals

### Abstraction
- **Abstraction** hides hardware complexity
- It provides simple and convenient interfaces
- Abstractions allow programmers to build complex systems more easily
- Common OS abstractions include processes, files, and virtual memory

### Performance
- The OS should provide high performance
- OS features introduce overhead:
  - extra CPU time
  - extra memory usage
  - extra disk usage
- The goal is to minimize overhead while providing useful functionality

### Protection and isolation
- The OS must protect applications from each other
- It must also protect itself from applications
- **Isolation** is the key mechanism for protection
- A faulty or malicious program should not harm others

### Reliability
- The OS should be highly reliable
- If the OS fails, all running applications fail
- Reliability is difficult due to system complexity

### Other design goals
- **Energy efficiency** is important for mobile systems
- **Security** extends protection against malicious behavior
- **Mobility** supports systems running on small or portable devices
- Different systems prioritize different goals

---
---

## Some History

### This section answers a very important question:
#### Why do modern operating systems look the way they do today?

### Early operating systems: simple libraries
- Early operating systems provided only basic functionality
- They were mainly libraries of commonly used routines
- Programs ran one at a time
- A human operator controlled job execution
- This model is known as **batch processing**
- There was little or no protection or isolation

### The need for protection
- Treating the OS as a simple library was unsafe
- Any program could access files, memory, or devices
- Privacy and safety could not be guaranteed
- This model did not scale to multi-user systems

### System calls and privilege levels
- The **system call** mechanism was introduced
- Programs run in **user mode** with restricted privileges
- The OS runs in **kernel mode** with full hardware access
- A system call uses a special instruction (**trap**) to enter the OS
- After servicing the request, control returns to user mode
- This enables protection and controlled access to hardware

### The era of multiprogramming
- Hardware became cheaper and more widely used
- I/O devices were slow compared to CPUs
- Running only one program wasted CPU time
- **Multiprogramming** allowed multiple programs in memory
- The OS switched between programs to improve CPU utilization

### New problems introduced
- Programs needed memory protection from each other
- Concurrency issues became common
- The OS had to handle interrupts correctly
- These challenges drove major OS innovations

### UNIX and consolidation of ideas
- UNIX combined many successful OS ideas
- It emphasized simplicity and powerful abstractions
- Written in C, making it portable and readable
- Encouraged sharing and collaboration
- Influenced most modern operating systems

### The PC era
- Early PC operating systems lacked protection
- A single faulty program could crash the system
- Over time, PC OSes adopted UNIX-like principles
- Modern desktop systems now include isolation and multitasking

### Linux and the modern world
- Linux reimplemented UNIX ideas in an open-source way
- It runs on servers, desktops, phones, and embedded systems
- Modern operating systems closely resemble classic UNIX designs

### Key takeaways
- Early OSes were simple and unsafe
- Protection required system calls and privilege separation
- Multiprogramming improved efficiency but added complexity
- UNIX unified core OS principles
- Modern OSes evolved by refining these ideas

---
---

# Chapter 2 — Exam Summary (Introduction to Operating Systems)

#### This summary answers one key goal:
What must I remember from Chapter 2 to answer exam questions correctly and quickly?

---

## What an operating system is
- An **operating system (OS)** is software that manages hardware resources
- It makes the system:
  - easy to use
  - efficient
  - safe
- The OS sits between applications and hardware

---

## Virtualization (core concept)
- The OS uses **virtualization** to manage resources
- Virtualization creates useful **illusions** for programs
- Main resources virtualized by the OS:
  - CPU
  - Memory
  - Storage (files)

---

## CPU virtualization
- A system may have only one physical CPU
- Multiple programs appear to run at the same time
- This is an illusion created by the OS
- At any instant, only one program runs on the CPU
- The OS rapidly switches between programs

Key terms:
- **CPU virtualization**
- **time sharing**
- **concurrency** (not parallelism)

Important distinction:
- Concurrency: programs make progress over time
- Parallelism: programs run simultaneously on multiple CPUs

---

## Memory virtualization
- Physical memory is shared
- Each process sees its own **private virtual address space**
- The same virtual address in different processes refers to different memory
- Programs cannot access each other’s memory

Key terms:
- **virtual address**
- **physical address**
- **address space**
- **memory isolation**

Why this matters:
- Safety
- Protection
- Simpler programming model

---

## Concurrency
- **Concurrency** occurs when multiple execution flows exist
- Threads within the same process share memory
- Shared memory introduces correctness problems

Key problem:
- Operations like increment are not **atomic**
- Instructions can interleave unpredictably
- Results may be wrong or inconsistent

Key terms:
- **thread**
- **shared memory**
- **atomicity**
- **non-determinism**

---

## Persistence
- Memory is **volatile**
- Data in memory is lost on crash or power failure
- Persistent storage is required for long-term data

Hardware:
- hard drives
- SSDs

Software:
- **file system** manages persistent data

Key properties:
- Data survives crashes and reboots
- Files are shared across processes
- Programs access storage via **system calls**

---

## System calls and protection
- Applications cannot access hardware directly
- Programs run in **user mode**
- The OS runs in **kernel mode**
- A **system call** uses a **trap** to enter the OS
- Control returns safely to user mode after service

Why this exists:
- Protection
- Isolation
- Controlled hardware access

---

## OS as a resource manager
- Resources:
  - CPU
  - memory
  - disk
- The OS decides:
  - who gets a resource
  - when
  - for how long
- Goals include:
  - efficiency
  - fairness
  - responsiveness

---

## Design goals of an OS
- **Abstraction**: hide complexity
- **Performance**: minimize overhead
- **Protection**: isolate programs
- **Reliability**: OS failure affects all programs

Additional goals (context-dependent):
- energy efficiency
- security
- mobility

---

## Historical evolution (conceptual)
- Early OSes were simple libraries
- Lack of protection caused problems
- **System calls** and privilege separation were introduced
- **Multiprogramming** improved CPU utilization
- Introduced new challenges:
  - concurrency
  - memory protection
- UNIX unified many core OS ideas
- Modern OSes reuse and refine these principles

---

## Exam checklist (use before submission)
- I can explain what virtualization means
- I know the difference between concurrency and parallelism
- I understand virtual memory vs physical memory
- I know why system calls exist
- I can explain why concurrency causes bugs
- I know why persistence is needed
- I can name core OS design goals

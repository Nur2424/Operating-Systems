Chapter 4, 5, 26, 27, 

## The Abstraction: The Process

This section answers a very important question:

What is a process, and how does the OS make programs actually run?

### Program vs process
- A **program** is a passive entity stored on disk
- It consists of instructions and static data
- A **process** is a **running program**
- The OS loads a program into memory and executes it as a process
- Only processes can use the CPU and system resources

### Why processes are needed
- Users want to run many programs at the same time
- Modern systems may run dozens or hundreds of processes
- The OS hides CPU availability from users
- The process abstraction makes systems easy to use

### Illusion of many CPUs
- Physical systems have only a few CPUs
- The OS provides the illusion of many CPUs
- This illusion is created through **CPU virtualization**
- Processes are the entities that are time-shared on the CPU

### Time sharing
- The OS runs one process, then stops it, then runs another
- This switching happens very quickly
- This technique is known as **time sharing**
- Time sharing enables concurrency, not true parallelism
- Sharing the CPU introduces performance trade-offs

### Mechanisms and policies
- **Mechanisms** are low-level tools that make virtualization possible
- An example mechanism is a **context switch**
- **Policies** decide which process should run next
- Scheduling policies use historical and workload information

### Time sharing vs space sharing
- **Time sharing** divides a resource over time (e.g., CPU)
- **Space sharing** divides a resource in space (e.g., disk blocks)

### Key takeaways
- A process is the fundamental execution unit managed by the OS
- Processes enable CPU virtualization
- Mechanisms define how the OS operates
- Policies define which decisions the OS makes

---
---

## The Abstraction: A Process (Machine State)

### This section answers a very important question:
#### What information defines a process at any moment in time?

### Machine state
- A **process** is a running program
- At any instant, a process is defined by its **machine state**
- Machine state includes everything the process can read or modify
- The OS uses machine state to manage and switch between processes

### Memory (address space)
- Program instructions reside in memory
- Program data is stored in memory
- Each process has its own **address space**
- The address space includes code, heap, stack, and static data
- Memory is part of the process state (see Chapter 2)

### Registers
- Registers hold execution context for the process
- Important registers include:
  - **Program Counter (PC)**: address of the next instruction
  - **Stack Pointer (SP)**: current position in the stack
  - **Frame Pointer (FP)**: manages function call frames
- Register values must be saved and restored during a context switch

### Persistent I/O state
- Processes often interact with storage and devices
- The OS tracks which files a process has open
- Open files and I/O metadata are part of process state

### Policy vs mechanism
- **Mechanisms** define how the OS performs actions
  - example: saving registers during a context switch
- **Policies** define which decision the OS makes
  - example: which process should run next
- Separating policy and mechanism improves modularity

### Key takeaways
- A process is fully described by its machine state
- The OS must preserve machine state to correctly resume execution
- Machine state is essential for context switching

---
---



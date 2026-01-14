## 4 The Abstraction: The Process

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

## 4.1 The Abstraction: A Process (Machine State)

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

## 4.2 Process API

### This section answers a very important question:
#### What basic operations must an operating system provide to manage processes?

- Every modern OS exposes a **process API**
- The process API allows users and programs to create and control processes
- These operations are conceptual and exist in all operating systems

### Create
- The OS must provide a way to create new processes
- Creating a process turns a program on disk into a running process
- Examples include running a command or starting an application

### Destroy
- The OS must be able to terminate processes
- Processes may exit normally when finished
- Processes may also be forcibly stopped if they misbehave

### Wait
- A process may need to wait for another process to finish
- Waiting is commonly used between parent and child processes
- Waiting supports coordination between processes

### Miscellaneous control
- The OS may allow a process to be temporarily stopped
- A stopped process can later be resumed
- This provides additional control over execution

### Status
- The OS provides information about running processes
- Examples include:
  - current process state
  - execution time
- Status information supports monitoring and management

### Program to process
- A program resides on disk as code and static data
- When executed, the OS loads the program into memory
- The OS allocates stack and heap space
- The result is a running process managed by the OS

### Key takeaways
- The process API defines how processes are managed
- Core operations include create, destroy, wait, control, and status
- These concepts apply across all modern operating systems

---
---

## 4.3 Process Creation: A Little More Detail

### This section answers a very important question:
#### How does the OS turn a program on disk into a running process?

### Loading code and static data
- A program initially resides on disk (or SSD)
- The OS reads the program’s code and static data into memory
- These are placed into the process’s **address space**
- Modern OSes often load code and data lazily
- Before execution, required program data must be in memory

### Setting up the run-time stack
- Each process has its own **run-time stack**
- The stack is used for:
  - function parameters
  - local variables
  - return addresses
- The OS allocates and initializes the stack
- The OS sets up arguments for `main()`:
  - `argc`
  - `argv`

### Setting up the heap
- The **heap** is used for dynamically allocated memory
- Programs request heap space using mechanisms such as `malloc`
- The heap starts small and can grow as needed
- The OS may allocate additional memory to satisfy heap requests

### Initializing I/O state
- The OS initializes I/O-related state for the process
- Processes typically start with standard input, output, and error
- Open files and I/O metadata are part of the process state

### Starting execution
- After setup is complete, the OS transfers control to the process
- Execution begins at the program’s entry point
- Typically, execution starts at the `main()` function
- The process is now ready to be scheduled on the CPU

### Key takeaways
- Process creation involves multiple OS-managed steps
- Code, stack, heap, and I/O state must be initialized
- Only after creation can a process be scheduled to run

---
---

## 4.4 Process States

### This section answers a very important question:
#### What can a process be doing at any moment in time?

### Core idea
- At any given time, a process is in a specific **state**
- The OS uses process states to manage execution and scheduling
- Process states describe whether a process can use the CPU or not

### The three fundamental process states

#### Running
- The process is currently executing on the CPU
- Instructions of the process are being executed
- On a single CPU, only one process can be running at a time

#### Ready
- The process is ready to run
- It has everything it needs to execute
- The OS has not chosen to run it yet
- Ready processes are eligible for scheduling

#### Blocked
- The process cannot run at the moment
- It is waiting for an external event
- A common reason is waiting for I/O to complete
- Blocked processes are not eligible to use the CPU

### State transitions

- **Ready → Running**
  - The process is scheduled by the OS
- **Running → Ready**
  - The process is descheduled or preempted
- **Running → Blocked**
  - The process initiates an I/O operation
- **Blocked → Ready**
  - The waiting event completes (e.g., I/O finished)

### Important notes
- A blocked process never transitions directly to running
- After becoming ready, a process must be scheduled to run
- State transitions involving the CPU are controlled by the scheduler

### Why blocking is important
- Blocking prevents wasting CPU time
- While one process waits for I/O, another can run
- This improves CPU utilization and system efficiency

### Key takeaways
- Processes move between running, ready, and blocked states
- Only running processes use the CPU
- Only ready processes can be scheduled
- I/O operations are the main cause of blocking
- The OS scheduler manages process execution

---
---

## 4.5 Data Structures

### This section answers a very important question:
#### How does the OS keep track of all processes and their state?

### Core idea
- The OS is a program and uses **data structures** to manage processes
- To manage multiple processes, the OS must track:
  - which processes exist
  - their current state
  - which process is running
  - which processes are blocked or ready

### Process list
- The OS maintains a **process list**
- The process list contains all processes in the system
- It allows the OS to:
  - find ready processes
  - identify blocked processes
  - select a process to run
- Any OS that supports multiprogramming has a structure like this

### Process Control Block (PCB)
- Information about each process is stored in a structure called the **Process Control Block (PCB)**
- The PCB represents one process
- The process list is a collection of PCBs

### What the PCB contains (conceptual)
- **Register context**
  - saved register values when the process is not running
  - used to resume execution
- **Process state**
  - running, ready, blocked, and other states
- **Process identifier (PID)**
- **Memory information**
  - address space size
  - memory pointers
- **Parent process**
- **Open files**
- **I/O-related information**

### Context switch support
- When a process is stopped:
  - its register context is saved into the PCB
- When a process resumes:
  - registers are restored from the PCB
- This mechanism enables **context switching**

### Additional process states
- Besides running, ready, and blocked:
  - a process may be in an initial state during creation
  - a process may be in a final state after exiting
- In UNIX-like systems, a finished but not yet cleaned-up process is called a **zombie**
- Zombies allow the parent to:
  - read the child’s exit status
  - then clean up the process using a wait operation

### Key takeaways
- The OS relies on data structures to manage processes
- Each process is represented by a PCB
- The process list tracks all active processes
- Saving and restoring PCB data enables context switching

---
---

# Chapter 4 — Exam 4.6 Summary (The Abstraction: The Process)

This summary answers one key exam goal:

What must I remember about processes to correctly answer exam questions?

---

## What a process is
- A **process** is a **running program**
- A program on disk is passive
- The OS loads a program into memory and runs it as a process
- Processes are the entities managed and scheduled by the OS

---

## Why processes exist
- Users want to run many programs at once
- The OS hides CPU availability from users
- Processes enable **CPU virtualization**
- Multiple processes give the illusion of many CPUs

---

## Process machine state (core concept)
A process is fully defined by its **machine state**, which includes:

- **Address space (memory)**
  - code
  - static data
  - heap
  - stack
- **Registers**
  - Program Counter (PC)
  - Stack Pointer (SP)
  - Frame Pointer (FP)
- **I/O state**
  - open files
  - file descriptors

The OS must save and restore this state to manage execution.

---

## Mechanism vs policy
- **Mechanism** answers: *how does the OS do something?*
  - example: context switching
- **Policy** answers: *which decision should the OS make?*
  - example: which process runs next
- Separating them improves modularity and flexibility

---

## Process API (conceptual)
Every OS provides basic operations to manage processes:

- **Create**: start a new process
- **Destroy**: terminate a process
- **Wait**: wait for another process to finish
- **Control**: suspend or resume execution
- **Status**: query process information

These are conceptual and OS-independent.

---

## Process creation (step-by-step)
To create a process, the OS:

1. Loads program code and static data from disk into memory
2. Creates and initializes the **run-time stack**
   - sets up `argc` and `argv`
3. Initializes the **heap**
4. Sets up **I/O state**
5. Transfers control to the entry point (`main()`)

Only after this can a process be scheduled.

---

## Process states (exam-critical)
In the simplified model, a process is in one of three states:

### Running
- Process is executing on the CPU
- Only one process can be running on a single CPU

### Ready
- Process is ready to run
- Waiting for the CPU
- Eligible for scheduling

### Blocked
- Process cannot run yet
- Waiting for an external event (usually I/O)

---

## State transitions
- **Ready → Running**: process is scheduled
- **Running → Ready**: process is descheduled/preempted
- **Running → Blocked**: process initiates I/O
- **Blocked → Ready**: event completes (e.g., I/O done)

Blocked processes never run directly.

---

## Data structures
- The OS maintains a **process list**
- Each process is represented by a **Process Control Block (PCB)**
- The PCB stores:
  - process state
  - register context
  - memory information
  - PID
  - open files
- Saving/restoring PCB data enables **context switching**

---

## Additional states (conceptual)
- Initial state during creation
- Final state after exit
- **Zombie**: process has exited but not yet cleaned up
- Zombies allow parents to collect exit status

---

## Scheduler connection
- The **scheduler** decides which ready process runs
- Scheduling decisions affect performance and responsiveness
- Scheduling is policy-driven

---

## Common exam traps
- Confusing **ready** with **blocked**
- Forgetting registers are part of process state
- Thinking blocked processes can run
- Ignoring the role of the PCB
- Mixing up program vs process

---

## Final checklist (use before exam)
- I can define a process clearly
- I know what machine state includes
- I understand process creation steps
- I can explain process states and transitions
- I know what a PCB is and why it exists
- I understand mechanism vs policy

---
---

## 5 Interlude: Process API

### This section answers a very important question:
#### How does a real operating system let programs create and control processes?

### What an interlude is
- Interludes focus on **practical aspects** of operating systems
- They emphasize **OS APIs** and how they are used
- They connect theoretical concepts to real system interfaces
- For the exam, interludes are important conceptually, not at code level

### Purpose of this interlude
- This interlude explains **process creation and control in UNIX systems**
- UNIX provides a small set of powerful system calls for process management
- These interfaces exist in some form in all modern operating systems

### Core UNIX process system calls
- **fork()**
  - creates a new process
- **exec()**
  - loads and runs a new program inside a process
- **wait()**
  - allows a process to wait for a child process to finish

These calls work together to implement process creation and control.

### Why this design matters
- UNIX separates:
  - creating a process
  - loading a program
- This separation provides flexibility and composability
- Simple primitives can be combined to create complex behavior

### The design question
- What interfaces should the OS expose for process creation?
- How should these interfaces balance simplicity and power?
- UNIX answers this with small, composable system calls

### Connection to previous chapters
- Chapter 4 explained what processes are and how the OS manages them
- Chapter 5 shows how programs request those operations from the OS
- System calls trigger the OS mechanisms studied earlier

### Key takeaways
- Process management is exposed through OS APIs
- UNIX uses fork, exec, and wait as core primitives
- These interfaces reflect deliberate OS design choices

---
---

## 5.1 The `fork()` System Call

### This section answers a very important question:
#### How does `fork()` create a new process, and why is it considered unusual?

### What `fork()` does
- `fork()` is used to create a **new process**
- The calling process becomes the **parent**
- The newly created process is the **child**
- After `fork()`, **both processes continue execution**

### Copying behavior
- The child is an almost exact copy of the parent
- The child receives:
  - its own **address space**
  - its own **registers**
  - its own **program counter**
  - its own **process identifier (PID)**
- Parent and child execute independently after creation

### Return values of `fork()`
- `fork()` returns different values in each process
- In the **child**, `fork()` returns `0`
- In the **parent**, `fork()` returns the PID of the child
- On failure, `fork()` returns a negative value
- Programs use this return value to distinguish parent from child

### Execution point
- The child does **not** start execution at `main()`
- The child begins execution **immediately after the `fork()` call**
- Code before `fork()` runs once
- Code after `fork()` may run twice

### Nondeterministic execution
- After `fork()`, both parent and child may be runnable
- On a single CPU, only one process runs at a time
- The **scheduler** decides which process runs first
- Output order is **nondeterministic**

### Relationship to scheduling
- Parent and child enter the ready state after `fork()`
- Scheduling decisions determine execution order
- Nondeterminism is expected and normal

### Key takeaways
- `fork()` creates a new process
- Parent and child continue from the same point
- Return values differ in parent and child
- Execution order after `fork()` is not predictable
- `fork()` does not start the child at `main()`

---
---

## 5.2 The `wait()` System Call

### This section answers a very important question:
#### How can a parent process wait for a child to finish execution?

### What `wait()` does
- `wait()` is called by a **parent process**
- It causes the parent to **block**
- The parent stops executing until a child process finishes
- When the child exits, `wait()` returns and the parent continues

### Effect on process states
- When the parent calls `wait()`:
  - Parent transitions from **Running → Blocked**
- The child continues executing
- When the child finishes:
  - The child exits
  - The parent is moved to **Ready**
  - The parent can run again when scheduled

### Deterministic execution
- Without `wait()`:
  - Parent and child execute concurrently
  - Output order is nondeterministic
- With `wait()`:
  - Parent cannot proceed until child finishes
  - The child always completes first
  - Output becomes deterministic

### Relationship to zombies
- When a child exits, it briefly becomes a **zombie**
- The zombie remains until the parent calls `wait()`
- `wait()` allows the OS to clean up the child’s resources
- Without `wait()`, zombie processes may accumulate

### Return behavior
- `wait()` returns when a child process finishes
- It returns the PID of the child that exited
- Exact return values and arguments are not required for the exam

### What `wait()` does not do
- `wait()` does not create processes
- `wait()` does not control when a child starts running
- `wait()` does not schedule processes
- It only controls when the parent resumes execution

### Key takeaways
- `wait()` blocks the parent process
- The parent waits until a child exits
- `wait()` makes execution order predictable
- `wait()` is essential for process synchronization and cleanup

---
---

## 5.3 The `exec()` System Call

### This section answers a very important question:
### How can a process run a different program after it has already been created?


### What `exec()` does
- `exec()` replaces the **current program** running in a process
- It does **not** create a new process
- The process continues to exist but runs a different program

### What changes after `exec()`
- The old program’s:
  - code
  - static data  
  are discarded
- The new program’s code and static data are loaded from disk
- The process’s:
  - address space is replaced
  - heap is re-initialized
  - stack is re-initialized
- A new argument list (`argv`) is set for the new program

### What does not change
- The **process identity** remains the same
- The process keeps:
  - the same **PID**
  - the same parent process
- Only the program changes, not the process

### Return behavior
- If `exec()` is successful, it **never returns**
- Execution continues in the new program
- Code after `exec()` runs only if `exec()` fails

### Relationship with `fork()`
- `fork()` creates a new process
- `exec()` replaces the program inside a process
- Together, they implement flexible process creation
- This design enables shells and pipelines

### Variants of `exec()`
- Multiple versions of `exec()` exist
- All variants perform program replacement
- Exact differences are not required for the exam

### Key takeaways
- `exec()` transforms the current process into a new program
- PID remains unchanged
- Address space is replaced
- Successful `exec()` never returns

---
---

## 5.4 Why? Motivating the Process API

### This section answers a very important design question:
### Why does UNIX separate process creation into `fork()` and `exec()`?

### The design problem
- At first glance, using both `fork()` and `exec()` seems unnecessary
- A single “create-and-run” call might appear simpler
- UNIX deliberately chose a different design

### Core idea: separation enables flexibility
- `fork()` creates a process
- `exec()` replaces the program inside a process
- Code can run **between** `fork()` and `exec()`
- This small gap enables powerful behavior

### The shell as motivation
- The shell is a user-level program
- When a command is executed, the shell:
  - calls `fork()` to create a child
  - modifies the child’s environment if needed
  - calls `exec()` to run the command
  - waits for the child to finish
- This pattern is central to UNIX process control

### Redirection
- Output redirection is implemented between `fork()` and `exec()`
- The shell modifies file descriptors before `exec()`
- The program writes to standard output without knowing it was redirected
- This works because the program inherits the modified environment

### Pipes
- Pipes connect the output of one process to the input of another
- Pipe setup occurs between `fork()` and `exec()`
- Simple primitives allow complex command composition

### Design philosophy
- The fork/exec interface is simple but powerful
- It enables composition rather than special-purpose features
- This reflects good system design principles

### Key takeaways
- Separating `fork()` and `exec()` is intentional
- The design allows flexibility and composability
- Many shell features rely on this separation

---
---

## 5.5 Other Parts of the Process API

### This section answers a general question:
#### Besides creating and running processes, how can the OS control and observe them?


### Core idea
- UNIX provides many additional interfaces for interacting with processes
- These interfaces support control, communication, and observation
- For the exam, only conceptual understanding is required

### Signals and `kill()`
- `kill()` is used to send **signals** to a process
- Signals are notifications or commands delivered to a process
- Signals can request actions such as:
  - terminate
  - stop (sleep)
  - resume
- The name `kill()` is misleading; it does not always terminate a process

### Signals subsystem
- Signals deliver external events to processes
- Processes can:
  - receive signals
  - handle signals
  - sometimes ignore signals
- Signals are a general mechanism for process control and communication

### Observing processes
- UNIX provides tools to observe running processes
- Examples include:
  - `ps`: lists processes
  - `top`: shows processes and resource usage
- These tools illustrate that process state is visible to users

### Design philosophy
- The OS exposes information and control to users
- Visibility helps with monitoring, debugging, and management
- More information about system activity is generally beneficial

### Key takeaways
- Process control goes beyond `fork()`, `exec()`, and `wait()`
- Signals are a key mechanism for controlling processes
- The OS provides interfaces and tools to observe process behavior

---
---

# Chapter 5 — Exam Summary (Interlude: Process API)

This summary answers one key exam goal:
How does a program create, control, and manage processes using OS interfaces?

---

## Purpose of the Process API
- The OS exposes a **process API** to user programs
- This API allows programs to:
  - create processes
  - control execution
  - synchronize with other processes
- Chapter 5 focuses on **UNIX-style process management**

---

## Core UNIX system calls (must know)

### `fork()`
- Creates a **new process**
- The new process is a **child**
- The calling process is the **parent**
- Parent and child both continue execution after `fork()`

Key facts:
- Child is an almost exact copy of the parent
- Child has its own:
  - PID
  - address space
- `fork()` return values:
  - `0` in the child
  - child PID in the parent
- Execution order after `fork()` is **nondeterministic**
- Child does **not** start at `main()`

---

### `wait()`
- Called by a **parent** process
- Causes the parent to **block**
- Parent waits until a child process finishes
- When the child exits, `wait()` returns

Key facts:
- While waiting, the parent does not run
- `wait()` makes execution order **deterministic**
- `wait()` allows cleanup of **zombie processes**
- `wait()` returns the PID of the finished child

---

### `exec()`
- Replaces the **current program** in a process
- Does **not** create a new process
- Loads a new program into the existing process

Key facts:
- PID remains the same
- Address space is replaced
- Heap and stack are re-initialized
- A successful `exec()` **never returns**
- Code after `exec()` runs only if `exec()` fails

---

## The fork–exec–wait pattern (very important)
- `fork()` → create child
- child optionally modifies environment
- `exec()` → run a new program
- parent calls `wait()`

This pattern is fundamental to:
- shells
- command execution
- pipelines
- redirection

---

## Why fork and exec are separate (design question)
- Separation allows code to run **between** process creation and program execution
- This enables:
  - I/O redirection
  - pipes
  - environment setup
- The design is flexible and composable

---

## Signals and additional process control
- UNIX provides more than fork/exec/wait
- `kill()` sends **signals** to processes
- Signals are general notifications, not just termination
- Signals allow processes to react to external events

---

## Observing processes
- The OS exposes information about running processes
- Tools such as `ps` and `top` show:
  - active processes
  - resource usage
- This reflects the OS design principle of visibility

---

## Common exam traps
- Thinking `fork()` creates a new program
- Thinking `exec()` creates a new process
- Forgetting that `exec()` never returns on success
- Forgetting that `wait()` blocks the parent
- Assuming deterministic output without `wait()`

---

## Final checklist (use before exam)
- I know what `fork()`, `wait()`, and `exec()` do
- I can explain parent vs child behavior
- I understand nondeterminism after `fork()`
- I know why `wait()` makes execution predictable
- I understand why UNIX separates fork and exec
- I know what signals are at a high level

---
---

# 26 Concurrency: An Introduction

### This section answers a fundamental question:
#### What changes when a program has more than one point of execution?

---

## Threads: a new abstraction

A **thread** is a single point of execution within a process.

- A traditional process has one thread of execution
- A multi-threaded process has multiple threads
- Each thread executes independently

---

## Threads vs processes

| Feature | Process | Thread |
|------|------|------|
| Address space | Private | Shared |
| Program counter | One | One per thread |
| Registers | One set | One set per thread |
| Stack | One | One per thread |
| Heap | Private | Shared |
| Isolation | Strong | Weak |

Threads are lighter than processes but more dangerous.

---

## Thread machine state

Each thread has its own execution state:
- program counter (PC)
- registers
- stack

Threads must be context-switched just like processes.

---

## Context switching between threads

- Switching threads requires saving and restoring registers
- The address space remains the same
- No page-table switch is needed

This makes thread switches faster than process switches.

---

## PCB vs TCB

- PCB (Process Control Block) stores process-wide state
- TCB (Thread Control Block) stores per-thread state
- A single process may have multiple TCBs

---

## Stack organization

### Single-threaded process
- One stack in the address space

### Multi-threaded process
- One stack per thread
- All stacks live in the same address space

Stack contents include:
- local variables
- function arguments
- return addresses

This is known as thread-local storage.

---

## Why threads are dangerous

- Threads share memory
- Execution order is unpredictable
- Threads can interfere with each other
- Bugs depend on timing and scheduling

This motivates the study of concurrency.

---

## Key takeaway

A thread is an execution unit inside a process; threads share memory but maintain independent execution state, making concurrency efficient but error-prone.

---
---

## 26.1 An Example: Thread Creation

### This section answers an important question:
#### What happens when we create threads, and why does execution order become unpredictable?

---

## Basic setup
- One main thread creates two worker threads
- Each worker thread runs the same function
- Each prints a different value ("A" or "B") and then exits
- The main thread waits for both threads using `pthread_join()`

---

## Thread creation
- Threads are created with `pthread_create()`
- After creation, a thread may:
  - start running immediately
  - or remain ready and run later
- There is no guarantee about when a thread will run

---

## Thread completion
- `pthread_join()` waits for a specific thread to finish
- The calling thread blocks until the target thread completes
- Similar in spirit to `wait()` for processes

---

## Nondeterministic execution order
- The order in which threads execute is not predictable
- A thread created first may run after a thread created later
- Output such as "A" and "B" may appear in any order

This behavior is due to the scheduler, not the program logic.

---

## Execution traces
- Multiple valid execution orders exist for the same program
- All shown interleavings are correct
- No single execution order can be assumed

---

## Thread creation vs function calls
- Creating a thread resembles a function call
- Unlike a function call:
  - the thread runs concurrently
  - it executes independently of the caller
  - it may run before or after the caller continues

---

## Early warning
- Even without shared data, threads introduce nondeterminism
- Reasoning about program behavior becomes harder
- This prepares for the next problem: shared data

---

## Key takeaway
Thread creation immediately introduces nondeterministic execution because threads run independently and are scheduled unpredictably.

---
---

## 26.2 Why It Gets Worse: Shared Data

### This section answers a critical question:
#### Why does concurrency become incorrect when threads share data?

---

## Shared data changes everything
- Threads share the same address space
- Shared variables can be accessed by multiple threads
- This introduces correctness problems, not just confusion

---

## The example
- Two threads update a global variable `counter`
- Each thread increments `counter` many times
- Expected result: `counter = 20,000,000`

---

## What actually happens
- The final value is often less than expected
- Each run may produce a different result
- The program is nondeterministic and incorrect

---

## Why this happens
- The operation `counter = counter + 1` is not atomic
- It consists of multiple steps:
  1. load the value from memory
  2. increment the value
  3. store the value back
- Threads can interleave these steps

---

## Race conditions
- A race condition occurs when multiple threads access shared data
- At least one access is a write
- The result depends on timing and scheduling

---

## Why a single CPU does not help
- Instructions can be interleaved even on one processor
- Context switches can occur between steps
- Parallel hardware is not required for races

---

## Nondeterminism
- The scheduler decides which thread runs
- Scheduling decisions vary between runs
- Small timing changes produce different outcomes

---

## Key insight
Correct-looking code can still be wrong under concurrency.

---

## Key takeaway
When threads share data, non-atomic operations can interleave unpredictably, leading to race conditions and incorrect results.

---
---

## 26.3 The Heart of the Problem: Uncontrolled Scheduling

### This section answers the central question of concurrency:
#### Why do correct-looking programs produce incorrect results under concurrency?

---

## The misleading simplicity
- A statement like `counter = counter + 1` looks simple
- It appears to be a single operation
- In reality, it is composed of multiple machine instructions

---

## What the CPU actually executes
Incrementing a shared variable typically involves:
1. loading the value from memory into a register
2. incrementing the register
3. storing the register back to memory

Each step is a separate instruction.

---

## Scheduling at instruction granularity
- The OS scheduler can interrupt a thread at any instruction
- On an interrupt:
  - the thread’s PC and registers are saved
  - another thread may run
- Threads can be interleaved between these instructions

---

## How the race occurs
- Thread 1 loads the value
- Thread 1 increments the value
- Thread 1 is interrupted before storing
- Thread 2 loads the old value
- Thread 2 increments and stores it
- Thread 1 resumes and stores its outdated value

The update from one thread is lost.

---

## Race condition
A **race condition** occurs when:
- multiple threads access shared data
- at least one thread writes to it
- the outcome depends on timing

The result becomes nondeterministic.

---

## Determinism vs indeterminism
- Without concurrency: same input → same output
- With uncontrolled scheduling: same input → different outputs
- Execution becomes indeterminate

---

## Critical section
- A **critical section** is code that accesses shared data
- It must not be executed by more than one thread at a time
- Failing to protect it leads to race conditions

---

## Mutual exclusion (conceptual)
- Mutual exclusion ensures only one thread executes a critical section
- This property is required for correctness
- How to enforce it is discussed later

---

## Key takeaway
Concurrency bugs arise because threads can be scheduled and interleaved at instruction boundaries, causing race conditions in non-atomic operations.

---
---

## 26.4 The Wish for Atomicity

### This section answers a natural question:
#### Why don’t we simply make shared-data operations atomic?

---

## The basic idea
- Race conditions occur because operations are not atomic
- A simple-looking statement expands into multiple instructions
- Context switches can occur between those instructions

---

## Atomicity
- An atomic operation executes as a single, indivisible unit
- It has no intermediate state
- It is either fully executed or not executed at all
- Interrupts can only occur before or after the operation

This is often described as “all or nothing”.

---

## The ideal (but unrealistic) solution
- Imagine a single instruction that updates memory atomically
- Such an instruction would eliminate races for that operation
- However, hardware cannot support atomic versions of all operations

---

## Why hardware alone is insufficient
- Complex operations cannot be made atomic in hardware
- Data structures such as trees or lists are too complex
- A general-purpose instruction set cannot provide atomic support for everything

---

## The real approach
- Hardware provides a small set of simple atomic primitives
- These primitives are combined with OS support
- Higher-level synchronization mechanisms are built on top

---

## Key concurrency terms

### Critical section
- Code that accesses a shared resource
- Must not be executed by more than one thread at a time

### Race condition
- Occurs when multiple threads enter a critical section
- At least one thread modifies shared data
- Outcome depends on timing

### Indeterminate program
- Program output varies from run to run
- Caused by race conditions
- Execution is not deterministic

### Mutual exclusion
- Ensures only one thread enters a critical section
- Prevents race conditions
- Leads to deterministic behavior

---

## Key takeaway
Because operations are not atomic, concurrent programs require synchronization and mutual exclusion to safely access shared data.

---
---

## 26.5 One More Problem: Waiting For Another

### This section introduces a second major class of concurrency problems:
#### How can one thread wait for another to complete an action before continuing?

---

## Waiting as a concurrency problem
- Not all concurrency issues involve shared data
- Some problems require correct ordering of execution
- A thread may need to wait until another thread finishes work

---

## Common examples
- A thread waits for disk I/O to complete
- A thread waits for another thread to produce data
- A thread must not proceed until a condition becomes true

---

## Why busy-waiting is bad
- Continuously checking a condition wastes CPU time
- It prevents efficient resource utilization
- Real systems must avoid spinning

---

## Sleeping and waking
- Threads should sleep when progress is impossible
- Threads should be woken when the needed event occurs
- This pattern is common in operating systems

---

## Different from atomicity
- Atomicity prevents race conditions on shared data
- Waiting ensures correct execution order
- Both are necessary for correct concurrent programs

---

## What this prepares for
- Future chapters introduce mechanisms to support waiting
- These include sleep/wakeup and condition variables
- This section explains why such mechanisms are needed

---

## Key takeaway
Concurrency requires not only atomic access to shared data but also mechanisms for threads to wait and coordinate their execution.

---
---

# Chapter 26 — Exam Summary  
## Concurrency: An Introduction

### This summary answers the main exam goal:

#### Why does concurrency make programs hard to reason about, and what core problems does it introduce?

---

## What concurrency is about
- Concurrency means **multiple threads executing within the same process**
- Threads:
  - share the same address space
  - run independently
  - are scheduled unpredictably
- Concurrency improves performance and responsiveness
- Concurrency introduces **correctness problems**

---

## Threads vs processes (core contrast)
- Processes are isolated by separate address spaces
- Threads share memory inside a process
- Threads are lighter and faster than processes
- Shared memory is the root of concurrency problems

---

## Nondeterminism
- The scheduler decides when each thread runs
- Execution order cannot be predicted
- Creation order does not imply execution order
- The same program can behave differently across runs

---

## Shared data and race conditions
- When threads access shared variables:
  - incorrect results can occur
- Even on a single CPU, races are possible
- The statement `counter = counter + 1` is **not atomic**
- Machine instructions can interleave in harmful ways

---

## Uncontrolled scheduling (the core problem)
- Threads can be interrupted at instruction boundaries
- Context switches occur transparently
- Interleaving causes lost updates
- Correct-looking code can be wrong

---

## Critical section
- A **critical section** is code that accesses shared data
- It must not be executed by more than one thread at a time
- Failing to protect it leads to race conditions

---

## Atomicity
- Atomic operations execute as indivisible units
- Atomicity prevents intermediate states
- Hardware cannot provide atomicity for all operations
- Only a small set of atomic primitives is feasible

---

## Mutual exclusion
- Mutual exclusion ensures:
  - only one thread enters a critical section
- It is required to avoid races
- It restores deterministic behavior

---

## Indeterminate programs
- Programs with race conditions are indeterminate
- Output varies from run to run
- Timing determines results
- Such bugs are difficult to debug and reproduce

---

## Waiting and coordination
- Concurrency problems are not only about shared data
- Threads may need to wait for other threads
- Busy-waiting wastes CPU time
- Threads must be able to sleep and be woken

---

## Two classes of concurrency problems
1. **Data access problems**
   - race conditions
   - atomicity
   - critical sections
2. **Coordination problems**
   - waiting for another thread
   - sleeping and waking
   - ordering constraints

---

## Why this is an OS topic
- The OS controls scheduling
- The OS provides atomic primitives
- The OS provides sleep/wakeup mechanisms
- Correct concurrency requires hardware and OS support

---

## Common exam traps
- Thinking a single CPU avoids races
- Assuming `counter++` is atomic
- Confusing atomicity with mutual exclusion
- Forgetting waiting is a separate problem
- Assuming deterministic output in concurrent programs

---

## One-sentence exam takeaway
Concurrency introduces nondeterminism and correctness challenges due to uncontrolled scheduling, shared data access, and the need for coordination between threads.

---

## Final checklist before the exam
- I can explain why threads are harder than processes
- I understand race conditions and critical sections
- I know why atomicity is required
- I can explain why waiting is a concurrency problem
- I understand why the OS is essential for concurrency

---
---

## 27.1 Thread Creation

This section explains how new threads are created and what information is required to start a thread.

---

## Purpose of thread creation
- Threads allow multiple execution flows within the same process
- Creating a thread starts a new concurrent execution context
- Threads share the same address space but execute independently

---

## What creating a thread means
When a thread is created:
- a new execution context is created
- the thread has its own:
  - program counter
  - registers
  - stack
- the thread shares:
  - code
  - heap
  - global variables
with other threads in the process

---

## Conceptual view of `pthread_create()`

Thread creation requires four pieces of information:

### Thread handle
- Used to identify the thread
- Allows later interaction with the thread (e.g., waiting for it)

### Attributes
- Specify optional properties of the thread
- Examples include stack size or scheduling priority
- Usually defaults are sufficient and no attributes are specified

### Start routine
- Specifies the function the thread will execute
- This function is the entry point of the thread
- It runs concurrently with other threads

### Argument
- A single argument passed to the thread function
- Allows data to be provided at thread start

---

## Use of `void *`
- Arguments and return values use `void *`
- This allows passing values of any type
- Threads cast the argument to the expected type internally

---

## Scheduling behavior
- A thread may start running immediately after creation
- Or it may be delayed and run later
- Execution order is determined by the scheduler
- Creation order does not imply execution order

---

## Key takeaway
Thread creation starts a new independent execution flow inside a process, sharing memory but running with its own stack and execution state.

---
---

## 27.1 Thread Creation
#### This section explains how new threads are created and what information is required to start a thread.

---

## Purpose of thread creation
- Threads allow multiple execution flows within the same process
- Creating a thread starts a new concurrent execution context
- Threads share the same address space but execute independently

---

## What creating a thread means
When a thread is created:
- a new execution context is created
- the thread has its own:
  - program counter
  - registers
  - stack
- the thread shares:
  - code
  - heap
  - global variables
with other threads in the process

---

## Conceptual view of `pthread_create()`

Thread creation requires four pieces of information:

### Thread handle
- Used to identify the thread
- Allows later interaction with the thread (e.g., waiting for it)

### Attributes
- Specify optional properties of the thread
- Examples include stack size or scheduling priority
- Usually defaults are sufficient and no attributes are specified

### Start routine
- Specifies the function the thread will execute
- This function is the entry point of the thread
- It runs concurrently with other threads

### Argument
- A single argument passed to the thread function
- Allows data to be provided at thread start

---

## Use of `void *`
- Arguments and return values use `void *`
- This allows passing values of any type
- Threads cast the argument to the expected type internally

---

## Scheduling behavior
- A thread may start running immediately after creation
- Or it may be delayed and run later
- Execution order is determined by the scheduler
- Creation order does not imply execution order

---

## Key takeaway
Thread creation starts a new independent execution flow inside a process, sharing memory but running with its own stack and execution state.

---
---

## 27.2 Thread Completion

This section explains how one thread can wait for another thread to finish execution.

---

## Why thread completion is needed
- Threads run independently once created
- The main thread does not automatically wait for other threads
- If the main thread exits, the entire process terminates
- Threads must be explicitly waited for to ensure correct execution

---

## `pthread_join()`
- `pthread_join()` allows one thread to wait for another thread
- The calling thread blocks until the target thread finishes
- This is the thread equivalent of `wait()` for processes

---

## Arguments to `pthread_join()`

### Thread identifier
- Specifies which thread to wait for
- The identifier is obtained during thread creation
- Allows waiting for a specific thread

### Return value pointer (optional)
- Threads can return a value when they finish
- The return value type is `void *`
- If the caller does not care about the return value, `NULL` can be passed

---

## Thread return values
- Threads return values using `void *`
- This allows returning any type of data
- Common pattern:
  - thread allocates memory
  - stores results in it
  - returns a pointer to the data
  - joining thread retrieves the pointer

---

## Blocking behavior
- The calling thread enters a blocked state
- No CPU is consumed while waiting
- The thread resumes when the target thread exits

---

## Relationship to process waiting
- `pthread_join()` is analogous to `wait()`
- Both:
  - block the caller
  - wait for completion
  - enable cleanup of execution state

---

## Key takeaway
`pthread_join()` allows a thread to block until another thread finishes, ensuring correct program completion and optional retrieval of results.

---
---

## 27.3 Locks (Conceptual Overview)

### This section introduces the basic mechanism used to enforce mutual exclusion in concurrent programs.

---

## Purpose of locks
- Locks are used to protect **critical sections**
- They ensure that **only one thread at a time** executes code that accesses shared data
- Locks prevent race conditions caused by concurrent access

---

## Mutual exclusion
- Mutual exclusion means that if one thread is executing a critical section:
  - no other thread may enter it
- Locks are the standard mechanism used to guarantee mutual exclusion

---

## Basic lock behavior
- A thread requests a lock before entering a critical section
- If the lock is free:
  - the thread acquires the lock and proceeds
- If the lock is held:
  - the thread waits until the lock is released
- After leaving the critical section:
  - the thread releases the lock

---

## What locks protect
- Shared variables
- Shared data structures
- Shared resources

Locks are not needed for:
- thread-local data
- private stack variables

---

## Relationship to earlier concepts
- Locks solve the problems identified in Chapter 26
- They prevent uncontrolled scheduling from corrupting shared data
- They restore deterministic behavior to concurrent programs

---

## Important notes
- Locks must be properly initialized before use
- Errors in lock usage can lead to incorrect behavior
- Correct use of locks is essential for program correctness

---

## Scope reminder
- This section introduces locks at a high level
- Detailed lock implementations and variants are studied later
- For now, focus on why locks exist, not how they are implemented

---

## Key takeaway
Locks enforce mutual exclusion by ensuring that only one thread at a time executes a critical section, preventing race conditions.

---
---

## Condition Variables — Explanation

Condition variables are used to coordinate execution between threads when one thread must wait for another thread to make progress before it can continue.

Locks alone are not sufficient for this purpose. Locks prevent multiple threads from entering a critical section at the same time, but they do not provide a way for one thread to sleep until a specific condition becomes true.

---

## What problem condition variables solve

Condition variables are needed when:
- a thread cannot continue yet
- it must wait until some condition becomes true
- another thread is responsible for making that condition true

Common examples include:
- waiting for data to be produced
- waiting for I/O to complete
- waiting for a buffer to become non-empty
- waiting for another thread to finish a step

Using busy-waiting (spinning) wastes CPU time and leads to poor performance and bugs. Condition variables allow threads to sleep efficiently instead.

---

## Core idea

A condition variable supports sleep–wake coordination between threads.

There are two roles:
- a waiting thread that sleeps until the condition becomes true
- a signaling thread that wakes waiting threads when the condition changes

Locks control *who* enters a critical section.  
Condition variables control *when* a thread is allowed to proceed.

---

## Relationship between condition variables and locks

Condition variables are never used alone.  
They are always associated with a lock (mutex).

The lock is required because:
- the condition depends on shared data
- shared data must be protected from race conditions
- checking the condition and going to sleep must be atomic

The lock protects:
- checking the condition
- modifying the condition
- signaling the condition

---

## How waiting works

Typical waiting sequence:
1. Acquire the lock
2. Check the condition
3. If the condition is false, call `pthread_cond_wait`
4. The thread releases the lock and goes to sleep
5. When woken, it re-acquires the lock
6. The thread re-checks the condition
7. If the condition is true, it continues
8. Release the lock

---

## Why the condition is checked in a while-loop

The condition must be checked in a `while` loop instead of an `if` statement because:
- threads can wake up spuriously
- multiple threads may be waiting
- another thread may consume the condition first

Waking up does not guarantee the condition is true.  
It only means that something *might* have changed.

---

## How signaling works

The signaling thread:
1. Acquires the same lock
2. Modifies shared state
3. Signals the condition variable
4. Releases the lock

Signaling does not transfer execution directly.  
It only wakes sleeping threads, which must then compete to acquire the lock.

---

## Relationship to earlier concurrency concepts

Condition variables build on:
- shared memory
- race conditions
- critical sections
- locks

They solve a different problem than locks:
- locks provide mutual exclusion
- condition variables provide ordering and waiting

They are almost always used together.

---

## Exam definition

A condition variable is a synchronization primitive that allows a thread to sleep until a specific condition becomes true and be safely woken by another thread, always in coordination with a lock.

---
---

## 27.5 Compiling and Running

This section explains how to correctly compile and run programs that use POSIX threads.

---

## Required header

Any program that uses threads must include the pthread header: #include <pthread.h>

This header provides:
- thread types (`pthread_t`)
- thread creation and control routines
- synchronization primitives such as locks and condition variables

---

## Required compiler flag

When compiling a threaded program, the pthread library must be explicitly linked using the `-pthread` flag.

Example compilation command: gcc -o main main.c -Wall -pthread

The `-pthread` flag:
- links the pthread library
- enables correct thread-related behavior in the compiler and runtime

Without this flag, the program may fail to link or behave incorrectly.

---

## Important note

Successfully compiling a threaded program does **not** mean it is correct.

Concurrency bugs such as race conditions may still exist even when:
- the program compiles successfully
- the program runs without crashing

---

## Exam takeaway

- `#include <pthread.h>` is required for threaded programs
- `-pthread` must be used during compilation
- compilation success does not guarantee correct concurrent behavior

---
---

# Chapter 27 — Thread API (Exam Summary)

This chapter introduces the POSIX thread API and explains how threads are created, controlled, synchronized, and coordinated. It provides the practical tools needed to solve the concurrency problems introduced in Chapter 26.

---

## Purpose of this chapter

Chapter 26 explains why concurrency is difficult:
- race conditions
- indeterminacy
- shared data corruption

Chapter 27 explains how programmers control concurrency using:
- thread creation
- thread completion
- locks
- condition variables

---

## Threads vs processes

A thread:
- is a unit of execution within a process
- shares the same address space as other threads
- has its own registers and stack

Threads:
- are cheaper than processes
- enable parallel execution within a program
- introduce shared-memory concurrency problems

---

## 27.1 Thread Creation

Threads are created using `pthread_create()`.

Key points:
- creating a thread is similar to calling a function
- the function runs concurrently
- scheduling is controlled by the OS
- execution order is not deterministic

Important details:
- threads start at a function pointer
- arguments and return values use `void *`
- threads may begin execution immediately or later

Exam focus:
- execution order cannot be assumed
- threads run independently once created

---

## 27.2 Thread Completion

Threads do not automatically synchronize with each other.

To wait for a thread to finish:
- use `pthread_join()`

Key points:
- `pthread_join()` blocks until the thread completes
- return values can be retrieved
- joining prevents lost results and zombies

Exam focus:
- thread creation does not imply thread completion
- joining is explicit and required

---

## 27.3 Locks (Mutexes)

Locks provide mutual exclusion for critical sections.

Purpose:
- protect shared data
- prevent race conditions

Key points:
- only one thread may hold a lock at a time
- other threads block until the lock is released
- lock/unlock must be paired

Important rules:
- locks must be initialized before use
- incorrect usage leads to bugs or deadlock

Exam focus:
- locks enforce mutual exclusion
- locks alone do not handle waiting

---

## 27.4 Condition Variables

Condition variables coordinate waiting between threads.

They are used when:
- a thread must wait for a condition to become true
- mutual exclusion alone is insufficient

Key ideas:
- always used with a lock
- waiting threads sleep efficiently
- signaling wakes waiting threads

Important details:
- conditions are checked in a `while` loop
- waking up does not guarantee the condition is true
- signaling is a hint, not a transfer of control

Exam focus:
- condition variables avoid busy waiting
- condition variables provide sleep/wakeup coordination

---

## 27.5 Compiling and Running

Threaded programs require explicit compiler support.

Requirements:
- include `<pthread.h>`
- compile with `-pthread`

Example: gcc -o main main.c -Wall -pthread

Important note:
- successful compilation does not guarantee correctness
- concurrency bugs occur at runtime

Exam focus:
- `-pthread` is mandatory
- compilation success ≠ correct concurrency

---

## Big picture

Conceptual flow:

Threads → Shared memory → Race conditions
Locks → Mutual exclusion
Condition variables → Waiting and coordination

---

## One-sentence exam summary

The POSIX thread API provides mechanisms to create, synchronize, and coordinate threads using joins, locks, and condition variables to safely control concurrent execution.








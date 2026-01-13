Chapter 4, 5, 26, 27, 

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

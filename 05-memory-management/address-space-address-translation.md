Combine:

	•	Ch 13 — Address spaces
	•	Ch 15 — Address translation (skim)
	•	Ch 18 — Paging
	•	Ch 21 — VM mechanisms
	•	Ch 22 — VM policies (skim)

Include:

	•	Logical vs physical address
	•	Page table
	•	Page fault
	•	Virtual memory
	•	Replacement intuition (FIFO, LRU)

---
---

# Chapter 13 — The Abstraction: Address Spaces

## Introduction

Early computer systems were simple because users expected very little: no interactivity, no protection, and no performance guarantees. Programs directly accessed physical memory, and the operating system provided minimal abstraction.

As systems evolved, users demanded:
- better performance
- interactivity
- reliability
- the ability to run multiple programs concurrently

These demands forced operating systems to introduce **memory abstractions**.  
The most important of these is the **address space**, which gives each process the illusion that it owns a private, contiguous memory, even though physical memory is shared.

This chapter explains **why address spaces are needed** and **how they emerged**, laying the foundation for later topics such as:
- address binding
- relocation
- protection
- virtual memory

---

## 13.1 Early Systems

In early systems, memory management was extremely simple.

Key characteristics:
- Physical memory was directly visible to programs
- The operating system resided at low physical addresses (e.g., starting at address 0)
- Only **one user program** ran at a time
- The program was loaded at a fixed physical address
- Programs used **physical addresses directly**

There was:
- no abstraction
- no isolation
- no protection between programs
- no relocation

This simplicity made OS development easy, but caused major limitations:
- CPU was idle during I/O
- Only one user/program at a time
- No safety: a program could overwrite the OS or itself

**Exam takeaway**:  
Early systems had **no address space abstraction** and **no memory protection**.

---

## 13.2 Multiprogramming and Time Sharing

### Multiprogramming

Because computers were expensive, systems evolved to improve CPU utilization.

Multiprogramming means:
- multiple processes are loaded in memory at the same time
- when one process blocks (e.g., for I/O), the OS switches to another
- CPU utilization increases

This required:
- context switching
- keeping multiple processes resident in memory

---

### Time Sharing

Later, users demanded **interactivity**:
- fast response times
- terminals instead of batch processing

Time sharing was introduced:
- each process runs for a short time slice
- the OS rapidly switches between processes
- gives the illusion that all programs run simultaneously

---

### A Naive (Incorrect) Approach

One possible approach:
1. Run one process
2. Save its entire memory to disk
3. Load another process’s memory from disk
4. Run it
5. Repeat

This approach is **far too slow**, because:
- saving/restoring full memory images is extremely expensive

---

### Key Insight

To support efficient time sharing:
- **processes must remain in memory**
- only CPU state (registers, PC, etc.) is switched
- memory contents are not copied to disk on each switch

This leads directly to the need for:
- memory protection
- isolation
- and the **address space abstraction**

**Exam takeaway**:  
Multiprogramming and time sharing require multiple processes to coexist in memory, which makes memory abstraction and protection mandatory.

---
---

## 13.3 The Address Space

### What is an Address Space?

An **address space** is the abstraction of memory that the operating system provides to a running process.

It represents **the process’s view of memory**, not the actual physical memory (RAM).  
Each process believes it has its own private memory, even though physical memory is shared among all processes.

This abstraction is fundamental to:
- memory virtualization
- protection
- multiprogramming
- time sharing

---

### What Does an Address Space Contain?

For understanding and exams, an address space is composed of **three main regions**:

1. **Code (Text Segment)**
2. **Stack**
3. **Heap**

Other regions exist (e.g., global/static variables), but these three are the core components.

---

### Code (Text Segment)

- Contains the **program instructions**
- Typically **static** (size does not change during execution)
- Loaded once when the program starts
- Easy for the OS to place in memory because it does not grow

---

### Stack

The **stack** is used to manage **function execution**.

It stores:
- function parameters
- local variables
- return addresses
- saved CPU registers

The stack **changes automatically** as functions are called and return.

#### What Is a Function Call? (IMPORTANT)

A **function call** occurs when a program transfers control to a function.

When a function is called:
1. The return address is saved
2. A new **stack frame** (activation record) is created
3. Local variables are allocated
4. Function parameters are passed

When the function returns:
- Its stack frame is removed
- Control goes back to the caller using the saved return address

This is why:
- The stack **grows** when functions are called
- The stack **shrinks** when functions return

#### Stack Growth Direction

In the example address space:
- The stack starts at the **bottom**
- It grows **upward** as function calls increase

---

### Heap

The **heap** is used for **dynamic memory allocation**.

It stores memory that:
- is explicitly requested by the program
- persists beyond a single function call
- must be manually managed by the programmer (or runtime)

Examples:
- `malloc()` / `free()` in C
- `new` / `delete` in C++
- objects in Java (managed by garbage collection)

#### Why Do We Need the Heap?

Local variables (stack):
- exist only during a function call
- are destroyed when the function returns

Heap memory:
- survives after the function returns
- allows flexible, long-lived data structures (lists, trees, buffers)

This is essential for:
- dynamic data structures
- variable-sized memory usage
- inter-function data sharing

#### Heap Growth Direction

In the example:
- The heap starts just after the code segment
- It grows **downward** as more memory is allocated

---

### Why Are Stack and Heap Placed at Opposite Ends?

Both stack and heap:
- grow dynamically
- have unpredictable size requirements

Placing them at opposite ends of the address space:
- maximizes available space
- delays collisions
- simplifies memory management

This layout is a **convention**, not a rule.

With **multithreading**, this simple layout often becomes insufficient.

---

### Virtual Addresses vs Physical Addresses (CRITICAL)

The addresses seen by a program (e.g., 0–16 KB) are **virtual addresses**.

They do **not** correspond directly to physical memory addresses.

Reality:
- The OS loads processes at arbitrary physical locations
- The program still thinks it starts at address 0

Example:
- Program accesses virtual address 0
- OS translates it to physical address (e.g., 320 KB)

This translation is done by:
- the operating system
- hardware support (MMU)

---

### What Does “Virtualizing Memory” Mean?

The OS **virtualizes memory** by:
- giving each process its own private address space
- making each process believe it owns the memory
- translating virtual addresses to physical ones transparently

From the program’s perspective:
- memory is private
- memory is contiguous
- other processes do not exist

This illusion is the foundation of modern operating systems.

---

### Principle of Isolation

**Isolation** means:
- one process cannot read or write another process’s memory
- one process cannot corrupt the OS

Benefits:
- faults are contained
- security is enforced
- system reliability increases

Modern OS designs (e.g., microkernels) push isolation even further by isolating OS components from each other.

---

### Exam-Oriented Summary

- An **address space** is a process’s view of memory
- It is an OS abstraction, not physical memory
- Main components:
  - code
  - stack
  - heap
- Stack:
  - supports function calls
  - stores local variables and return addresses
- Heap:
  - supports dynamic memory allocation
  - survives beyond function calls
- Programs generate **virtual addresses**
- OS translates them to **physical addresses**
- This is called **memory virtualization**
- Isolation is a primary goal of address spaces

---
---

# Chapter 13 — The Abstraction: Address Spaces (Exam Summary)

## Big Picture
This chapter introduces **address spaces**, the key abstraction used by the OS to **virtualize memory**.  
An address space is the **process’s view of memory**, not the real physical layout.  
Understanding this chapter is essential for:
- address binding
- virtual memory
- paging
- protection & isolation
- many exam questions on memory

---

## 13.0 Introduction — Why Address Spaces Exist
Early computers were simple:
- one program in memory
- OS at a fixed physical address
- no protection, no abstraction

As systems evolved:
- machines became expensive → shared by multiple programs
- multiprogramming and time sharing appeared
- memory needed to be shared **safely and efficiently**

This forced the OS to create a **clean abstraction of memory**.

---

## 13.1 Early Systems
Characteristics:
- Physical memory directly visible to programs
- OS loaded at low physical addresses
- One running program at a time
- No isolation, no protection

Limitations:
- Only one program could safely run
- A buggy program could overwrite the OS
- No support for multiprogramming

👉 Important takeaway:  
**There was no address space abstraction** — programs used physical memory directly.

---

## 13.2 Multiprogramming and Time Sharing
### Multiprogramming
- Multiple processes resident in memory at the same time
- CPU switches when a process blocks (e.g., I/O)
- Improves CPU utilization

### Time Sharing
- CPU rapidly switches between processes
- Goal: **interactivity**
- Users perceive the system as responsive

Key challenge:
- Saving entire memory to disk on each switch is too slow
- Processes must **remain in memory**
- OS must manage memory shared among processes

👉 This motivates the need for **memory virtualization**.

---

## 13.3 The Address Space
### Definition
An **address space** is:
> The abstraction of memory provided by the OS to a running program.

It represents **all memory state** of a process.

---

### Main Components of an Address Space
1. **Code (Text Segment)**
   - Program instructions
   - Usually static
   - Placed at a fixed region

2. **Stack**
   - Used for **function calls**
   - Stores:
     - local variables
     - function parameters
     - return addresses
   - Grows when functions are called
   - Shrinks when functions return

   **Function call reminder (exam-relevant):**
   - A function call pushes a new *stack frame*
   - Contains arguments + return address
   - This is why recursion uses more stack space

3. **Heap**
   - Used for **dynamic memory allocation**
   - Managed by the programmer (e.g., `malloc`, `new`)
   - Grows when memory is allocated
   - Shrinks when memory is freed

   **Heap reminder (exam-relevant):**
   - Used for data whose lifetime is not tied to function calls
   - Common source of memory leaks and fragmentation

---

### Stack vs Heap (Very Exam-Friendly)
| Stack | Heap |
|---|---|
| Automatic allocation | Manual allocation |
| Function-related | Data-structure-related |
| Grows with calls | Grows with malloc/new |
| Fast | More flexible |

---

### Layout Convention
Typical layout:
- Code at one end
- Heap grows in one direction
- Stack grows in the opposite direction
- Free space in between

This is a **convention**, not a rule.

---

## Virtual vs Physical Addresses
- Programs use **virtual addresses**
- Physical memory layout is hidden
- Same virtual address (e.g., 0) maps to **different physical addresses** for different processes

This mapping is handled by:
- OS + hardware (MMU)

👉 This is what we mean by **virtualizing memory**.

---

## Key Term: Virtualizing Memory
The OS:
- gives each process the illusion of a **private, large memory**
- maps virtual addresses to physical memory
- enables multiple processes to coexist safely

This is the foundation of **modern operating systems**.

---

## 13.4 Goals of Virtual Memory
### 1. Transparency
- Programs are unaware of virtualization
- No changes needed to application code
- Memory looks private and contiguous

### 2. Efficiency
- Programs should not run much slower
- OS should not waste memory
- Requires hardware support (e.g., TLBs)

### 3. Protection
- Processes cannot access each other’s memory
- Processes cannot access OS memory
- Invalid accesses are blocked

---

## Isolation (Critical Concept)
Protection enables **isolation**:
- Each process runs in its own sandbox
- Bugs or malicious behavior are contained
- Improves reliability and security

Microkernels push isolation even further by isolating OS components.

---

## Core Exam Takeaways
- Address space = abstraction of memory
- Virtual addresses ≠ physical addresses
- Stack → function calls
- Heap → dynamic allocation
- Virtual memory goals:
  - Transparency
  - Efficiency
  - Protection
- Protection ⇒ isolation

---

## One-Line Exam Answer
**An address space is the OS abstraction that gives each process the illusion of a private, contiguous memory, enabling multiprogramming, protection, and efficient execution.**

---
---

# Chapter 15 — Mechanism: Address Translation (Introduction)

## Big Picture: Why Address Translation Exists

Programs behave as if:
- They have their **own private memory**
- Their code starts at **virtual address 0**
- No other program can access their memory

Reality:
- Many programs run at the same time
- All share the same **physical RAM**
- The CPU switches rapidly between them

**Address translation** is the mechanism that makes this illusion possible.

---

## Link to Limited Direct Execution (LDE)

Address translation follows the same philosophy as CPU virtualization:

- Programs run directly on hardware most of the time
- The OS intervenes only at **critical moments**
  - system calls
  - timer interrupts
  - faults

For memory:
- Hardware performs fast translations
- OS configures and controls the rules
- Result: **efficiency + control**

---

## What Is Address Translation?

**Address translation** is the process by which hardware:
- Converts a **virtual address** (used by a program)
- Into a **physical address** (real RAM location)

This happens on **every memory access**:
- instruction fetch
- load
- store

The program never sees physical memory.

---

## Virtual vs Physical Address (EXAM-CRITICAL)

- **Virtual address**
  - Generated by the program
  - Part of the program’s address space
  - Example: address 0

- **Physical address**
  - Actual location in RAM
  - Managed by the OS
  - Example: address 320 KB

Example:
- Program loads from virtual address `0`
- Hardware translates it to physical address `320 KB`
- Program is unaware of the translation

This illusion is the foundation of **virtual memory**.

---

## Why Hardware Support Is Required

If the OS handled every translation:
- It would be far too slow
- Every memory access would trap into the kernel

Therefore:
- **Hardware performs translations**
- **OS sets up and manages translation rules**

Think of it as:
- Hardware = fast translator
- OS = manager and protector

---

## Role of the Operating System

The OS must:
- Decide which virtual addresses map to which physical addresses
- Track free and used memory
- Configure translation hardware
- Enforce protection between processes

Hardware alone cannot provide memory virtualization.

---

## Core Goals of Address Translation

### 1. Efficiency
- Most translations handled in hardware
- Minimal OS involvement during execution

### 2. Control
- OS restricts what memory each process can access
- Prevents memory corruption between processes

### 3. Flexibility
- Programs organize memory as they want
- Stack, heap, and code layout are abstracted
- OS adapts underneath

---

## Key Exam Takeaways

You must be able to explain:
- What address translation is
- Virtual vs physical address difference
- Why hardware support is essential
- How this fits the limited direct execution model
- How address translation enables isolation and protection

---
---

## 15.1 Assumptions

To introduce address translation, the OS makes simplifying assumptions to avoid unnecessary complexity. These assumptions are temporary and will be relaxed later.

### Assumption 1: Contiguous Physical Memory
- Each process’s entire address space is placed in **one contiguous block** of physical memory
- No paging or fragmentation handling
- Enables simple translation mechanisms

### Assumption 2: Address Space Fits in Physical Memory
- Virtual address space size < physical memory size
- No swapping or disk involvement
- Every memory reference can be satisfied from RAM

### Assumption 3: Equal-Sized Address Spaces
- All processes have the same virtual address space size (e.g., 16 KB)
- Eliminates variable-size management complexity

### Why These Assumptions Matter
- Allow the OS to **relocate** a process anywhere in physical memory
- Process still believes its address space starts at virtual address 0
- Sets up the core virtualization problem: illusion vs reality

### Common Exam Pitfalls
- Treating these assumptions as realistic
- Confusing contiguous virtual memory (always true) with contiguous physical memory (assumed here)
- Forgetting these constraints are later removed


## 15.2 An Example

This example motivates the need for address translation using a simple code sequence.

### Process View: Virtual Address Space

- Virtual address space: 0 → 16 KB
- Layout:
```
0 KB
+———------------———+
| Program Code     |
|                  |
+———------------———+
| Heap (grows up)  |
+—————------------—+
|     Free         |
|                  |
+———------------———+
| Stack (grows down)|
+————------------——+
16 KB
```
- Variable `x`:
  - Stored at virtual address **15 KB**
  - Initial value = 3000

### Example Code

C-level:
```c
x = x + 3;
```
```
Assembly-level:

128: movl 0x0(%ebx), %eax   ; load x
132: addl $0x03, %eax      ; add 3
135: movl %eax, 0x0(%ebx)  ; store x
```
### Virtual Memory References (Process Perspective)

- Fetch instruction at address 128
- Load from address 15 KB
- Fetch instruction at address 132
- Fetch instruction at address 135
- Store to address 15 KB

All addresses above are **virtual addresses.**

### Physical Reality

- OS occupies low physical memory
- Process cannot be loaded at physical address 0
- Process is relocated to physical address 32 KB
```
Physical Memory
0 KB
+------------------+
| Operating System |
+------------------+
|   Free           |
+------------------+
| Code             |
| Heap             |
| Stack            |
+------------------+
|   Free           |
+------------------+
64 KB
```
### The Core Problem
- Program assumes addresses start at 0
- Physical memory placement starts elsewhere
- OS must:
- Relocate process
- Preserve illusion of a zero-based address space
- Avoid modifying program code

### Key Mechanism: Interposition
- Hardware sits between CPU and memory
- Every memory reference is translated:
- Instruction fetch
- Load
- Store
- Translation is transparent to the process

### Why This Matters
- Enables safe relocation
- Enables memory virtualization
- Foundation for all modern virtual memory systems

### Common Exam Pitfalls
- Ignoring instruction fetches during translation
- Assuming compiler knows physical addresses
- Missing transparency as a key benefit

---
---

## 15.3 Dynamic (Hardware-based) Relocation

Dynamic relocation is the first hardware-supported mechanism for address translation.  
It is also called the **base and bounds** approach.

---

### Motivation
- Programs are written assuming they start at virtual address 0
- OS may load a process anywhere in physical memory
- Need:
  - **Relocation**: move processes freely
  - **Protection**: prevent illegal memory access
- Must be **transparent** to the process

---

### Base and Bounds Registers

Each CPU contains two hardware registers (set by the OS on context switch):

#### Base Register
- Holds the **starting physical address** of the process
- Example: `base = 32 KB`

#### Bounds (Limit) Register
- Holds the **size of the virtual address space**
- Example: `bounds = 16 KB`
- Used for protection

---

### Address Translation Rule

For every memory reference:

1. **Bounds check**
   - Ensure: `0 ≤ virtual_address < bounds`
   - If false → hardware exception (fault)

2. **Translation**  physical_address = virtual_address + base

Applies to:
- Instruction fetch
- Load
- Store

Translation happens **at runtime** → dynamic relocation.

---

### Example: Instruction Execution

Given:
- Base = 32 KB
- Bounds = 16 KB

Virtual addresses:
- Instruction at 128
- Variable `x` at 15 KB

Steps:
- Fetch instruction:
- `128 + 32 KB = 32896`
- Load `x`:
- `15 KB + 32 KB = 47 KB`
- Store `x`:
- Same translation again

Every access goes through base + bounds.

---

### Example Address Translations

Given:
- Base = 16 KB
- Bounds = 4 KB

| Virtual Address | Physical Address | Result |
|-----------------|------------------|--------|
| 0               | 16 KB            | OK     |
| 1 KB            | 17 KB            | OK     |
| 3000            | 19384            | OK     |
| 4400            | —                | Fault (out of bounds) |

---

### Why Bounds Are Necessary

- Prevents access to:
- Other processes’ memory
- Operating system memory
- Any address:
- `< 0` or `≥ bounds` → hardware fault
- Protection enforced by **hardware**, not software

---

### Static vs Dynamic Relocation

#### Static Relocation (Software-based)
- Loader rewrites addresses before execution
- Problems:
- No protection
- Hard to relocate later
- Unsafe

#### Dynamic Relocation (Hardware-based)
- Addresses translated at runtime
- Advantages:
- Strong protection
- Easy relocation
- Full transparency

---

### Memory Management Unit (MMU)

- Hardware component that performs address translation
- Contains base and bounds logic
- Becomes more complex in advanced memory systems

---

### Bounds Register: Two Interpretations

1. **Size-based (used here)**
- Check: `virtual_address < bounds`
- Then add base

2. **End-address-based**
- Add base first
- Check against physical end address

Both are equivalent logically.

---

### OS Responsibility: Free List (Aside)

- OS must track unused physical memory
- Simplest structure: **free list**
- List of free physical memory ranges
- Used to decide where to load processes

---

### Common Exam Pitfalls

- Forgetting bounds check happens **before** translation
- Ignoring instruction fetches in translation
- Thinking base/bounds are per process (they are per CPU, per running process)
- Confusing static relocation with dynamic relocation
- Assuming relocation happens only once (it happens on every access)

---
---

## 15.4 Hardware Support: A Summary

This section summarizes the hardware features required to support **dynamic (base-and-bounds) relocation** safely and efficiently.

---

### Dual CPU Modes

The CPU must support at least two execution modes:

#### Kernel (Privileged) Mode
- Used by the operating system
- Full access to hardware
- Can execute privileged instructions

#### User Mode
- Used by applications
- Restricted access
- Cannot modify critical hardware state

A **mode bit** (e.g., in the process status word) indicates the current mode.  
Mode switches occur on system calls, interrupts, or exceptions.

---

### Base and Bounds Registers

- Hardware must provide:
  - **Base register**: starting physical address of the process
  - **Bounds (limit) register**: size of the process’s virtual address space
- Registers are part of the **Memory Management Unit (MMU)**
- One pair per CPU
- Set by the OS during a context switch

---

### Hardware Address Translation

For every memory reference:
- Instruction fetch
- Load
- Store

The hardware must:
1. Check that the virtual address is within bounds
2. Translate the address: physical_address = virtual_address + base

This process is automatic and occurs on every access.

---

### Privileged Instructions to Update Base and Bounds

- CPU must provide special instructions to:
- Set base register
- Set bounds register
- These instructions are:
- **Privileged**
- Executable only in kernel mode

This prevents user programs from disabling memory protection.

---

### Exception Handling Support

The CPU must raise exceptions when:
- A virtual address is out of bounds
- A user program attempts a privileged instruction

On exception:
- CPU switches to kernel mode
- Execution transfers to an OS-defined exception handler
- OS typically terminates the offending process

---

### Registering Exception Handlers

- OS must be able to inform the CPU where exception handlers reside
- Requires additional privileged instructions
- User programs must not be able to install handlers

---

### Hardware Requirements Summary (Figure 15.3)

| Hardware Requirement | Purpose |
|---------------------|---------|
| Privileged mode | Prevent user programs from executing privileged operations |
| Base/bounds registers | Support address translation and bounds checking |
| Translation & bounds circuitry | Perform virtual-to-physical mapping and validation |
| Privileged instructions to update base/bounds | Allow OS to safely manage relocation |
| Privileged instructions to register handlers | Allow OS to handle exceptions correctly |
| Ability to raise exceptions | Enforce memory protection and isolation |

---

### Common Exam Pitfalls

- Forgetting that mode separation is required in addition to bounds checking
- Assuming base/bounds registers are per process instead of per CPU
- Ignoring instruction fetches during address translation
- Not mentioning exceptions in protection mechanisms

---
---

## 15.5 Operating System Issues

With hardware support for dynamic (base-and-bounds) relocation, the operating system still has key responsibilities. Hardware performs fast address translation, but the OS manages memory and processes at specific control points.

---

### OS Responsibilities Summary (Figure 15.4)

| OS Requirement | Notes |
|---------------|-------|
| Memory management | Allocate memory for new processes; reclaim memory from terminated processes; manage free memory using a free list |
| Base/bounds management | Save and restore base and bounds registers on context switches |
| Exception handling | Handle exceptions; typically terminate misbehaving processes |

---

### 1. Process Creation: Memory Allocation

When a process is created, the OS must:
- Find a contiguous region of physical memory for the process
- Use a **free list** to locate available memory
- Mark the selected region as allocated
- Record the base address for the process

Under current assumptions:
- All address spaces are the same size
- All fit into physical memory

This simplifies allocation to slot-based placement.

---

### 2. Process Termination: Memory Reclamation

When a process terminates (normally or forcibly), the OS must:
- Reclaim all physical memory used by the process
- Return the memory region to the free list
- Clean up associated process metadata

Failure to reclaim memory results in OS-level memory leaks.

---

### 3. Context Switches: Base and Bounds Management

Key constraint:
- Each CPU has only **one** base/bounds register pair

Therefore, on a context switch:

#### Stopping a Process
- Save base and bounds values
- Store them in the process control block (PCB)

#### Starting or Resuming a Process
- Load the process’s base and bounds values into CPU registers

Incorrect handling leads to memory corruption or crashes.

---

### 4. Relocating a Stopped Process

If a process is not running, the OS may:
- Copy its entire address space to a new physical location
- Update the saved base value in the PCB

When the process resumes:
- New base is restored
- Process is unaware it was moved

This is possible because relocation is dynamic.

---

### 5. Exception Handling

Exceptions occur when:
- A process accesses memory outside its bounds
- A process attempts to execute a privileged instruction

On exception:
1. CPU switches to kernel mode
2. Control transfers to an OS exception handler
3. OS typically terminates the offending process
4. OS frees memory and cleans up process state

---

### 6. Limited Direct Execution Model

- Processes run directly on the CPU most of the time
- OS intervenes only:
  - on process creation
  - on termination
  - on context switches
  - on exceptions

This minimizes overhead while preserving protection.

---

### Common Exam Pitfalls

- Assigning memory allocation to hardware instead of the OS
- Forgetting to save/restore base and bounds on context switches
- Assuming memory is reclaimed automatically
- Mixing hardware and OS responsibilities

---
---

## Chapter 15 Summary — Address Translation

This chapter extends the idea of **limited direct execution** into the domain of memory by introducing **address translation**, a core mechanism of virtual memory.

---

### Address Translation

- Processes generate **virtual addresses**
- Hardware translates virtual addresses into **physical addresses**
- Translation occurs on:
  - Instruction fetch
  - Load
  - Store
- Translation is:
  - Fast (performed in hardware)
  - Transparent (process is unaware)

This creates the illusion that:
- Each process has its own private memory
- Each process’s address space starts at 0

---

### Base and Bounds (Dynamic Relocation)

The chapter focuses on a simple virtualization mechanism:

- **Base register**
  - Holds the starting physical address of the process
- **Bounds (limit) register**
  - Holds the size of the process’s virtual address space

For every memory reference:
1. Hardware checks that the virtual address is within bounds
2. Hardware computes: physical_address = virtual_address + base

Because translation happens at runtime, this mechanism is called **dynamic relocation**.

---

### Efficiency and Protection

- Base-and-bounds is efficient:
- One addition
- One comparison per memory access
- Provides strong **memory protection**:
- Processes cannot access OS memory
- Processes cannot access other processes’ memory
- Illegal accesses raise hardware exceptions handled by the OS

Protection is essential for system safety and control.

---

### Hardware and OS Cooperation

#### Hardware Responsibilities
- Perform address translation
- Enforce bounds checking
- Raise exceptions on illegal accesses
- Support privileged and user modes

#### Operating System Responsibilities
- Allocate physical memory to processes (via a free list)
- Reclaim memory on process termination
- Save and restore base and bounds on context switches
- Handle exceptions, usually by terminating offending processes

---

### Limitation: Internal Fragmentation

- Each process is placed in a fixed-size physical memory slot
- Heap and stack may use only part of the allocated space
- Unused memory within the slot is wasted

This waste is called **internal fragmentation**:
- Free memory exists but cannot be used effectively

---

### Why a More Advanced Mechanism Is Needed

- Base-and-bounds is:
- Simple
- Fast
- Protective
- But it is:
- Too coarse-grained
- Inefficient in memory usage

To reduce internal fragmentation and better utilize memory, more sophisticated techniques are required.

---

### What Comes Next

- The next step is **segmentation**
- Segmentation generalizes base-and-bounds
- Provides more flexible memory allocation
- Reduces internal fragmentation

---

### Key Exam Takeaways

- Difference between virtual and physical addresses
- Base + bounds translation rule
- Hardware vs OS responsibilities
- Importance of protection
- Meaning of internal fragmentation
- Motivation for segmentation

---
---



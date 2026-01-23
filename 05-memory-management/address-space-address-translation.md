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




<img width="462" height="651" alt="Снимок экрана 2026-01-23 в 10 28 30" src="https://github.com/user-attachments/assets/4b3e245f-e469-4b78-98e8-248ad0b3f858" />

## Chapter 21 — Beyond Physical Memory: Mechanisms (Introduction)

This introduction explains **why virtual memory must extend beyond physical RAM** and motivates the need for mechanisms such as swap space and page faults.

---

### Assumptions Being Dropped

Up to this point, we assumed:
- Each process’s entire address space fits in physical memory
- All pages of all processes are always resident in RAM

These assumptions are **unrealistic** for real systems.

---

### The Real Problem

Modern systems must support:
- Many concurrently running processes
- Large virtual address spaces per process
- Limited physical memory

Result:
- Physical memory **cannot hold all pages** of all processes at the same time

---

### Adding a New Level to the Memory Hierarchy

To address this, the OS introduces an additional storage level that is:

- Much **larger** than RAM
- Much **slower** than RAM

In practice:
- This role is served by a **hard disk** or **SSD**
- It sits below main memory in the hierarchy

Conceptual hierarchy: CPU => Main Memory (RAM) => Secondary Storage (Disk / Swap) 

---

### The Central Question (The Crux)

How can the OS:
- Use a **large, slow storage device**
- To **transparently** provide the illusion of
- A **large virtual address space**?

Key idea:
- Transparency means the program and programmer are unaware of paging to disk

---

### Why Large Address Spaces Matter

Large virtual address spaces provide:
- Ease of programming
- No need for programmers to manage memory placement
- Natural allocation of data structures as needed

Contrast with older systems:
- Used **memory overlays**
- Required manual movement of code/data in and out of memory
- Error-prone and inconvenient

---

### Multiprogramming Connection

- Running many programs concurrently increases memory demand
- Early systems could not keep all needed pages in RAM
- Swapping pages to disk became necessary

Thus:
- Multiprogramming + ease of use
→ need for **beyond-physical-memory mechanisms**

---

### Scope of This Introduction

This section:
- Explains **why** disk-backed virtual memory is needed
- Sets up the motivation for swap space and page faults

It does **not yet** define:
- Present bit
- Page faults
- Replacement policies

Those mechanisms are covered in later sections.

---

### Key Takeaway

Because physical memory is limited, the OS uses a large, slower storage device to extend memory and transparently provide the illusion of a large virtual address space.

---
---

## 21.1 Swap Space

This section introduces **swap space**, the first key mechanism that allows virtual memory to extend beyond physical RAM.

---

### Definition of Swap Space

- **Swap space** is a reserved area on disk used by the operating system to:
  - Swap pages **out of physical memory** to disk
  - Swap pages **into physical memory** from disk
- Pages are moved in **page-sized units**
- Swap space is:
  - Much **larger** than RAM
  - Much **slower** than RAM
- Completely managed by the OS

---

### Why Swap Space Is Needed

Without swap space:
- All pages of all processes must fit in physical memory
- Limits:
  - Process size
  - Number of concurrent processes

With swap space:
- Only a subset of pages must reside in RAM
- Remaining pages can be stored on disk
- The OS provides the **illusion of more memory than physically available**

This is a core requirement for virtual memory.

---

### OS Responsibilities When Using Swap

To support swapping, the OS must:
- Track which pages are in physical memory
- Track which pages are on disk
- Remember the **disk location** of swapped-out pages
- Move pages between: RAM ↔ Swap Space

This information is later tied to:
- Page tables
- Present bit
- Page faults

---

### Swap Space Size

- Swap space size determines:
- The maximum number of pages the system can manage
- How many processes can exist concurrently
- In this chapter:
- Swap space is assumed to be **very large**

---

### Example Interpretation (Figure 21.1)

The example shows:
- **4 physical frames** in RAM
- **8 blocks** of swap space on disk

Process states:
- Some processes have:
- A few pages in RAM
- Remaining pages in swap
- One process has:
- All pages swapped out
- Not currently running
- One swap block is still free

This demonstrates:
- A process does not need all pages in RAM to exist
- Physical memory can be shared among multiple processes
- Swap space allows memory oversubscription

---

### Swap Space vs Other Disk Locations

- Swap space is **not the only disk source** for pages
- Code pages:
- Originate from executable files on disk
- Can be reloaded from the file system if evicted
- Heap and stack pages:
- Typically stored in swap space when evicted

This distinction is conceptual and not heavily tested.

---

### Exam-Relevant Points

You should know:
- What swap space is
- Why it is required for virtual memory
- That pages can exist entirely on disk
- That a process does not need to be fully resident in RAM

You are **not** expected to know:
- Swap implementation details
- Disk layout specifics

---

### Key Takeaway

Swap space is disk storage used by the OS to hold pages not currently in physical memory, enabling virtual memory systems to support large address spaces and many concurrent processes.

---
---

## 21.2 The Present Bit

This section explains **how the system determines whether a page is in physical memory or on disk**, and precisely **when a page fault occurs**.

---

### Motivation

Once pages can be swapped to disk, the hardware and OS must know:
- Whether a referenced page is currently resident in **physical memory**
- Or stored on **disk (swap or file system)**

This information is provided by the **present bit** in each page table entry (PTE).

---

### Memory Reference Recap

On every memory reference:

1. The process generates a **virtual address**
2. Hardware extracts the **Virtual Page Number (VPN)**
3. Hardware checks the **TLB**
   - **TLB hit** → immediate translation, memory access
   - **TLB miss** → consult the page table in memory

This behavior is unchanged from Chapter 18.

---

### Role of the Present Bit

When the page table entry (PTE) is accessed, the **present bit** determines the next step.

#### Case 1: Present Bit = 1
- The page is **resident in physical memory**
- The PTE contains a valid **Physical Frame Number (PFN)**
- Hardware:
  - Loads the PFN into the TLB
  - Retries the instruction
- Execution continues normally

---

#### Case 2: Present Bit = 0
- The page is **not in physical memory**
- The page resides on **disk**
- Hardware cannot complete the access

➡️ A **page fault** is generated

---

### Definition of a Page Fault (Exam-Critical)

A **page fault** occurs when:
- A process accesses a **valid virtual page**
- But the page is **not currently present in physical memory**

Important distinctions:
- Page fault ≠ illegal memory access
- Page fault ≠ segmentation fault
- Page fault ≠ invalid page

The page belongs to the process’s address space but is temporarily not in RAM.

---

### Why It Is Called a “Fault”

- Accessing a swapped-out page is a **legal operation**
- However, the hardware does not know how to retrieve the page
- The hardware raises an **exception** and transfers control to the OS
- Because this mechanism is the same as for illegal operations, it is called a *fault*

Conceptually:
- A page fault is similar to a **cache miss handled by the OS**

---

### What Happens Next (Preview)

- On a page fault:
  - Control transfers to the OS
  - The **page-fault handler** runs
- The handling process is described in later sections

---

### Exam-Relevant Points

You should be able to answer:
- What the present bit indicates
- When a page fault occurs
- The difference between:
  - invalid pages
  - pages not present in memory
- Why page faults are expensive (disk access involved)

---

### Key Takeaway

The present bit indicates whether a page resides in physical memory; accessing a valid page whose present bit is zero triggers a page fault and transfers control to the operating system.

----
---

## 21.3 The Page Fault

This section explains **how the operating system handles a page fault**, what actions are taken, and why page faults are managed in software rather than hardware.

---

### Definition Recap

A **page fault** occurs when:
- A process accesses a **valid virtual page**
- The page is **not present in physical memory**
- The page resides on **disk** (swap space or file)

---

### Who Handles Page Faults

- Systems may use:
  - Hardware-managed TLBs
  - Software-managed TLBs
- **In all systems**, page faults are handled by the **operating system**
- The OS executes a special routine called the **page-fault handler**

Page faults are **always handled in software**, not directly by hardware.

---

### OS Actions on a Page Fault

When a page fault occurs, the OS must:

1. Identify that the page is not present
2. Determine **where the page is stored on disk**
3. Issue a disk I/O request to fetch the page
4. Place the page into physical memory
5. Update the page table entry (PTE):
   - Set the **present bit** to 1
   - Update the **PFN**
6. Retry the faulting instruction

The instruction that caused the page fault is restarted transparently.

---

### Locating the Page on Disk

- When a page is not present:
  - The PFN field in the PTE is not used as a frame number
  - Instead, it stores the **disk address** of the page
- The OS reads this information from the PTE to locate the page on disk

Key idea:
- A PTE stores **either** a PFN (if present) **or** a disk address (if not present)

---

### Why Hardware Does Not Handle Page Faults

Page faults are handled by the OS because:

1. **Disk I/O is extremely slow**
   - Software overhead is negligible compared to disk access time
2. **Simplicity and flexibility**
   - Hardware would need to understand swap space and disk I/O
   - OS-level handling keeps hardware simpler

Thus, hardware raises an exception and transfers control to the OS.

---

### Process State During a Page Fault

- While the disk I/O is in progress:
  - The faulting process is placed in the **blocked** state
- The OS can schedule other ready processes
- This overlap improves CPU utilization in multiprogrammed systems

---

### Exam-Relevant Points

You should be able to explain:
- What a page fault is
- Who handles it and why
- How the OS finds the page on disk
- What happens to the process during the fault
- Why page faults are expensive

---

### Key Takeaway

A page fault is handled by the OS, which retrieves the missing page from disk, updates the page table, and restarts the instruction while the process remains blocked during the disk I/O. 


---
---

## 21.4 What If Memory Is Full?

This section explains **what the operating system must do when a page fault occurs and physical memory has no free frames available**. It introduces the concept of **page replacement** and its impact on performance.

---

### The Problem Scenario

In earlier sections, page fault handling assumed:
- At least one free physical frame exists
- The OS can immediately load the missing page into memory

In reality:
- Physical memory may be **full or nearly full**
- No free frame is available to load the faulting page

---

### OS Action When Memory Is Full

When a page fault occurs and memory is full, the OS must:

1. **Select a page currently in physical memory**
2. **Evict (page out) that page**
   - Write it to disk if necessary
3. **Free the physical frame**
4. **Load the required page into memory**
5. Update page tables accordingly

This process is unavoidable in virtual memory systems.

---

### Page Replacement

The act of choosing which page to evict is governed by a:

👉 **Page-Replacement Policy**

A page-replacement policy defines:
- Which page should be removed from physical memory
- When memory is needed for another page

Examples (covered in the next chapter):
- FIFO
- LRU
- Clock / Second Chance

---

### Why Page Replacement Is Critical

Replacing the wrong page can cause severe performance problems:

- The evicted page may be needed again immediately
- This leads to:
  - Repeated page faults
  - Excessive disk I/O
- Execution speed may degrade to **disk speed**

Important performance insight:
- Disk access is **10,000–100,000× slower** than memory access

---

### What This Section Establishes

This section emphasizes:
- Page replacement is **required** when memory is full
- Replacement decisions affect **performance**, not correctness
- The quality of the replacement policy is crucial

It does **not** yet explain:
- How specific replacement algorithms work
- How to choose the “best” page

Those topics are covered in Chapter 22.

---

### Exam-Relevant Points

You should be able to answer:
- What happens if a page fault occurs when memory is full
- Why a page must be evicted
- What a page-replacement policy is
- Why poor replacement decisions are costly

---

### Key Takeaway

When physical memory is full and a page fault occurs, the operating system must evict an existing page using a page-replacement policy to make room for the required page.

---
---

## 21.5 Page Fault Control Flow

This section describes the **complete control flow of a memory access** when paging and swapping are enabled. It combines hardware actions and operating system actions into a single coherent sequence.

---

### Purpose of This Section

This section answers the question:

> What happens when a program accesses memory?

It explains all possible outcomes of a memory reference, depending on:
- TLB state
- Page table contents
- Validity and residency of the page

---

## Hardware Control Flow (During Address Translation)

When a memory access occurs and the translation is **not found in the TLB**, the hardware consults the page table. Three cases are possible.

---

### Case 1: Page Is Valid and Present

- The page belongs to the process
- The page is resident in physical memory

Hardware actions:
- Reads the physical frame number from the page table entry
- Inserts the translation into the TLB
- Retries the instruction
- The retry results in a TLB hit
- Execution continues normally

---

### Case 2: Page Is Valid but Not Present

- The page belongs to the process
- The page is currently stored on disk

Hardware actions:
- Raises a page fault
- Transfers control to the operating system

This is the standard virtual memory case.

---

### Case 3: Page Is Invalid

- The page does not belong to the process
- Often caused by an illegal memory access or program bug

Hardware actions:
- Traps the invalid access
- Transfers control to the operating system trap handler
- The operating system typically terminates the process

In this case, the present bit is irrelevant.

---

## Software Control Flow (Operating System Page-Fault Handler)

When the operating system receives a page fault, it performs the following steps:

1. Finds a free physical frame  
   - If no free frame exists, the operating system runs a page replacement policy to evict a page
2. Issues a disk input/output request to read the missing page from disk
3. Updates the page table entry:
   - Sets the present bit to true
   - Stores the physical frame number
4. Restarts the faulting instruction

The program is unaware that any of these steps occurred.

---

## Interaction with the Translation Lookaside Buffer

After the page is loaded into memory:

- The first retry of the instruction may cause a translation lookaside buffer miss
- The translation is inserted into the translation lookaside buffer
- A subsequent retry results in a translation lookaside buffer hit
- The memory access finally completes successfully

---

## Process State During a Page Fault

- While disk input/output is in progress:
  - The faulting process is placed in the **blocked** state
- The operating system schedules other ready processes
- This overlap improves CPU utilization in multiprogrammed systems

---

## Exam-Relevant Distinctions

You must clearly distinguish between:
- A valid and present page
- A valid but not present page
- An invalid page

And understand:
- Which cases cause a page fault
- Which cases cause process termination
- Which components are handled by hardware and which by the operating system

---

### One-Sentence Takeaway (Exam-Ready)

On a memory access, hardware handles translation lookaside buffer hits and valid-present pages, raises a page fault for valid-but-absent pages, and traps illegal accesses; the operating system page-fault handler then loads the page, updates the page table, and restarts the instruction.

---
---

## 21.6 When Replacements Really Occur

This section explains **when page replacement actually happens in real operating systems**, correcting the simplified assumption that replacement only occurs when physical memory is completely full.

---

### Unrealistic Assumption in Simplified Models

Earlier descriptions assumed:
- The operating system waits until memory is entirely full
- A page fault occurs
- A page is then immediately evicted to make room

This approach is unrealistic and inefficient in real systems.

---

### Proactive Memory Management

Modern operating systems try to:
- Keep **some free physical pages available at all times**
- Avoid performing page replacement in the critical path of a page fault

To do this, they rely on predefined thresholds.

---

### High Watermark and Low Watermark

The operating system defines two thresholds:

- **Low watermark (LW)**  
  The minimum number of free pages the system wants to maintain

- **High watermark (HW)**  
  The desired number of free pages after memory has been reclaimed

Operation:
- When free pages fall below the low watermark, memory reclamation begins
- Pages are evicted until the number of free pages reaches the high watermark

---

### Background Paging Thread

When memory drops below the low watermark:
- A **background thread** is activated
- Often referred to as the page daemon or swap daemon

Responsibilities:
- Evict pages from physical memory
- Free multiple pages in advance
- Go back to sleep once enough memory has been freed

This allows page replacement to occur **before it is urgently needed**.

---

### Performance Benefits

Performing replacements in the background enables:
- Reduced blocking during page faults
- Better system responsiveness
- Improved disk efficiency

---

### Clustering of Page Writes

When evicting pages in batches, the operating system can:
- Group multiple pages together
- Write them to disk in a single operation

This improves performance by:
- Reducing disk seek time
- Reducing rotational delay
- Increasing overall disk throughput

---

### Interaction with Page Fault Handling

With background replacement:
- A page fault handler first checks for a free physical frame
- If no free frame exists, it signals the background paging thread
- Once free pages are available, the faulting page can be loaded

This separates:
- Page replacement activity
- Page fault servicing

---

### Exam-Relevant Points

You should understand that:
- Page replacement does not wait until memory is completely full
- Operating systems proactively evict pages
- High and low watermarks control replacement behavior
- Background paging improves performance and reduces latency

---

### One-Sentence Takeaway (Exam-Ready)

Operating systems perform page replacement proactively using background threads and high and low watermarks, evicting pages before memory is exhausted to ensure free frames are available and to improve performance.

---
---

# Chapter 21 — Beyond Physical Memory: Mechanisms  
## Big Picture Summary (Exam-Oriented)

Up to this point in memory management, we assumed that **all pages of all processes fit into physical memory**. Chapter 21 removes this assumption and explains **how operating systems provide the illusion of very large virtual address spaces even when physical memory is limited**.

The key idea is simple but powerful:

> The operating system uses **disk space** as an extension of physical memory and transparently moves pages between memory and disk as needed.

This chapter explains the **mechanisms** that make this possible (not the policies for choosing which page to evict — those come next).

---

## Why We Need to Go Beyond Physical Memory

Modern systems:
- Run many processes concurrently
- Each process may require a large address space
- Physical memory is limited and expensive

Without virtual memory:
- Programs would have to manually manage memory
- Programmers would need to load and unload code/data explicitly (overlays)
- Programming would be complex and error-prone

Virtual memory solves this by giving each process:
- A **large, contiguous virtual address space**
- The illusion that “memory is always available”

---

## Swap Space: Extending Memory Using Disk

To support address spaces larger than physical memory, the OS reserves part of the disk called **swap space**.

Swap space is used to:
- Store pages that are not currently needed in memory
- Move pages **out of memory (swap out)** and **back into memory (swap in)**

Important properties:
- Swap space is accessed in **page-sized units**
- It determines the **maximum number of pages** the system can manage
- Pages may also originate from files (e.g., executable code), not only swap

Key intuition:

> Memory is fast and small; disk is slow and large — the OS uses both.

---

## The Present Bit: Knowing Where a Page Is

To support swapping, the page table needs more information.

Each page table entry includes a **present bit**:
- Present = 1 → page is in physical memory
- Present = 0 → page is on disk

If a process accesses a valid page that is **not present**, this triggers a **page fault**.

Important distinction:
- Page fault ≠ illegal access  
- A page fault can occur on a **legal page** that is simply not in memory

---

## What a Page Fault Really Is

A **page fault** occurs when:
- The page is valid
- The page is not in physical memory
- The hardware cannot complete the memory access

When this happens:
- Hardware raises an exception
- Control transfers to the **operating system**
- A **page-fault handler** runs

Page faults are handled **entirely by software**, even on systems with hardware-managed TLBs.

Why software?
- Disk I/O is extremely slow
- Hardware complexity would be excessive
- Software overhead is negligible compared to disk latency

---

## Page Fault Handling: What the OS Does

When servicing a page fault, the OS must:

1. Determine where the page is located on disk  
   (often stored in the page table entry)
2. Find a physical frame to load the page into
3. If memory is full, evict another page
4. Read the page from disk into memory
5. Update the page table:
   - Mark page as present
   - Store the physical frame number
6. Restart the faulting instruction

While disk I/O is in progress:
- The faulting process is **blocked**
- The CPU runs another ready process

This overlap is essential for performance.

---

## What If Memory Is Full?

If no free physical frames exist when a page fault occurs:
- The OS must **evict a page**
- The decision of which page to evict is governed by a **page replacement policy**

Key point of this chapter:
- Replacement **exists**
- Replacement decisions strongly affect performance

Details of how pages are chosen (FIFO, LRU, etc.) are deferred to the next chapter.

---

## The Complete Page Fault Control Flow

When a memory access happens, there are **three possible outcomes** after a TLB miss:

1. **Valid and present page**
   - Translation is loaded into the TLB
   - Instruction is retried and succeeds

2. **Valid but not present page**
   - Page fault is raised
   - OS loads the page from disk
   - Instruction is retried

3. **Invalid page**
   - Illegal memory access
   - OS trap handler runs
   - Process is usually terminated

This division is critical and commonly examined.

---

## When Replacements Actually Occur (Reality vs Theory)

In theory:
- Replacement happens only when memory is full

In reality:
- Operating systems **proactively free memory**

They use:
- A **low watermark** (too few free pages)
- A **high watermark** (enough free pages)

When free memory drops below the low watermark:
- A **background paging thread** runs
- Pages are evicted in advance
- Memory is freed until the high watermark is reached

This avoids:
- Blocking page faults
- Doing slow work on the critical path

---

## Background Paging and Performance

Background replacement allows:
- Batch eviction of pages
- Grouped disk writes (clustering)
- Reduced seek and rotational delays
- Better disk throughput

This is a classic OS optimization:

> Do slow work in the background, not when urgently needed.

---

## The Core Mental Model to Remember

When revising, remember this **end-to-end story**:

- Virtual memory allows address spaces larger than physical memory
- Disk (swap space) is used to store inactive pages
- Page tables track whether pages are in memory or on disk
- A page fault occurs when a valid page is not present
- The OS handles page faults by loading pages from disk
- Replacement may be needed if memory is full
- Real systems evict pages proactively using background threads

---

## Final Exam-Ready Takeaway

> Chapter 21 explains how operating systems use disk as an extension of memory, detect missing pages via the present bit, handle page faults in software, evict pages when necessary, and proactively manage memory using background replacement to maintain performance.


---
---

# Chapter 22 — Beyond Physical Memory: Policies  
## Introduction and 22.1 Cache Management

This chapter focuses on **how the operating system decides which page to evict from memory** when physical memory is under pressure. While previous chapters explained the *mechanisms* of virtual memory, this chapter explains the *policies* that make those mechanisms perform well.

---

## Memory Pressure and the Need for Policies

When plenty of free physical memory is available:
- A page fault occurs
- The operating system finds a free physical page
- The missing page is loaded
- Execution continues smoothly

When physical memory becomes scarce:
- The operating system must **page out** one or more pages
- Choosing the wrong page to evict can severely degrade performance

This decision-making logic is called the **replacement policy** of the operating system.

---

## The Central Problem of This Chapter

> How can the operating system decide which page (or pages) to evict from memory?

This decision:
- Is made whenever memory pressure exists
- Has a massive impact on performance
- Was historically critical when systems had very little physical memory

---

## Viewing Physical Memory as a Cache

A key conceptual shift introduced in this chapter is:

> Physical memory can be viewed as a **cache** for virtual memory pages stored on disk.

Under this model:
- Disk acts as the backing store
- Physical memory acts as a cache
- Pages are the cached items

Consequences:
- Accessing a page already in memory is a **cache hit**
- Accessing a page that must be fetched from disk is a **cache miss**

This analogy is fundamental to understanding page replacement.

---

## Goal of a Replacement Policy

A replacement policy aims to:

- **Minimize the number of cache misses**  
  (i.e., page faults that require disk access)

Or equivalently:
- **Maximize the number of cache hits**  
  (i.e., accesses satisfied from physical memory)

This goal exists because disk access is extremely slow compared to memory access.

---

## Average Memory Access Time (AMAT)

To reason about performance, we use **Average Memory Access Time (AMAT)**.

Conceptually, AMAT combines:
- The probability of a memory access being a hit
- The probability of a memory access being a miss
- The cost of accessing memory
- The cost of accessing disk

The intuition behind AMAT is more important than the formula itself.

---

## Why Miss Rate Dominates Performance

Typical access costs:
- Memory access: on the order of **nanoseconds**
- Disk access: on the order of **milliseconds**

This means disk access is roughly **100,000 times slower** than memory access.

As a result:
- Even a very small miss rate can dominate total execution time
- Programs with frequent page faults effectively run at disk speed

Key insight:
> Avoiding page faults is far more important than optimizing memory hits.

---

## Implications for System Design

Because disk access is so expensive:
- Replacement policies must be carefully designed
- Keeping frequently-used pages in memory is critical
- Poor policies can make a fast system behave like a very slow one

This explains why:
- Page replacement is a central operating system problem
- Many different replacement policies exist

---

## How This Section Connects to What Follows

This conceptual framework prepares you to understand:
- First-In First-Out replacement
- Least Recently Used replacement
- Approximations of usage history
- Thrashing and system collapse under heavy memory pressure

All of these topics attempt to solve the same problem:
- Keeping the right pages in memory at the right time

---

## Exam-Relevant Points

You should be able to explain:
- Why physical memory is treated as a cache
- What cache hits and cache misses mean in virtual memory
- Why disk access dominates performance
- Why replacement policy choice is crucial

You are not expected to:
- Memorize numerical constants
- Derive formulas under time pressure
- Understand hardware cache internals

---

## One-Sentence Takeaway (Exam-Ready)

Replacement policies aim to minimize page faults by treating physical memory as a cache for disk-resident pages, because even a small miss rate causes severe performance degradation due to extremely slow disk access.

---
---

## 22.2 The Optimal Replacement Policy

This section introduces the **optimal page-replacement policy**, a theoretical policy that achieves the **minimum possible number of cache misses** for a given sequence of memory accesses. Although it cannot be implemented in practice, it is essential as a **baseline for comparison**.

---

### Purpose of the Optimal Policy

The optimal policy exists to answer the question:

> What is the best possible replacement behavior if the future were known?

It is **not** intended to be used in real operating systems. Instead, it provides:
- A lower bound on the number of misses
- A reference point to evaluate real policies

---

### Definition

The **optimal replacement policy** evicts:

> **The page whose next access is farthest in the future.**

If a page is **never accessed again**, it is always the best choice to evict.

This policy results in the **fewest possible cache misses** for any given reference string.

---

### Intuition Behind Optimality

When a replacement is required:
- Pages that will be used soon should be kept
- Pages that will be used farthest in the future are least useful now

By evicting the page with the most distant next use:
- All other pages will be accessed earlier
- Future misses are minimized

No other policy can outperform this behavior on the same access sequence.

---

### Why the Optimal Policy Cannot Be Implemented

The critical limitation is that:
- The policy requires **perfect knowledge of future memory accesses**

In real systems:
- The future reference string is unknown
- Programs may behave unpredictably

Therefore:
- The optimal policy is **not deployable**
- It is used only for **analysis and comparison**

---

### Why the Optimal Policy Is Still Useful

Despite being impractical, the optimal policy is valuable because:
- It provides a **gold standard**
- It allows meaningful evaluation of real policies
- It shows how close a policy is to the theoretical best

Without such a reference, hit rates and miss counts lack context.

---

### Tracing the Optimal Policy (Exam-Relevant Skill)

To simulate the optimal policy on a reference string:

1. Start with an empty cache
2. On each page access:
   - If the page is in cache → hit
   - If not → miss
3. If a miss occurs and the cache is full:
   - Look ahead in the reference string
   - Determine the next access of each cached page
   - Evict the page whose next use is farthest in the future
   - If a page is never used again, evict it

You must be able to perform this reasoning manually.

---

### Compulsory (Cold-Start) Misses

The example introduces **compulsory misses**:
- The first access to a page always causes a miss
- These misses are unavoidable

Such misses are sometimes excluded when comparing policies to focus on replacement behavior.

---

### Types of Cache Misses (Context)

In general, cache misses can be classified as:

1. **Compulsory (cold-start) miss**  
   First reference to a page

2. **Capacity miss**  
   Cache cannot hold all needed pages

3. **Conflict miss**  
   Due to placement restrictions

Important note for paging:
- OS page caches are **fully associative**
- Conflict misses do **not** occur in paging systems

---

### Exam-Relevant Points

You should be able to explain:
- What the optimal replacement policy is
- Why it minimizes misses
- Why it cannot be implemented
- Why it is used as a comparison baseline
- What compulsory misses are
- How to trace optimal replacement on a page-reference sequence

---

### One-Sentence Takeaway (Exam-Ready)

The optimal replacement policy evicts the page whose next access is farthest in the future, achieving the minimum possible number of misses and serving as a theoretical benchmark despite being impossible to implement in real systems.

---
---

## 22.3 A Simple Policy: FIFO

This section introduces **FIFO (First-In, First-Out)** page replacement, one of the simplest replacement policies historically used in operating systems.

---

### Basic Idea of FIFO

FIFO replaces pages based solely on **arrival time** in memory.

How it works:
- When a page is loaded into memory, it is placed at the **end of a queue**
- Pages remain in the queue in the order they entered memory
- When a replacement is needed:
  - The page that has been in memory the **longest time** (the first one inserted) is evicted

FIFO does **not** consider:
- How often a page is accessed
- How recently a page is accessed
- Whether a page is important or heavily used

---

### Strength of FIFO

FIFO’s main advantage:

- **Simplicity**

It is:
- Easy to understand
- Easy to implement
- Requires minimal bookkeeping

This is why many early systems used FIFO.

---

### FIFO Behavior on a Reference String

When tracing FIFO:
- The first accesses to distinct pages cause **compulsory misses**
- Pages are added to the queue in arrival order
- Once the cache is full, every miss causes eviction of the **oldest page**

Replacement rule:
- Always evict the page that entered memory first

Even if that page:
- Was accessed recently
- Is frequently used

---

### Performance Characteristics

FIFO often performs **poorly** compared to optimal replacement because:

- It cannot distinguish important pages from unimportant ones
- It may evict heavily-used pages simply because they are old
- It ignores locality of reference

As a result:
- Hit rates are often significantly lower than optimal
- Performance can degrade badly under some workloads

---

### Comparison with Optimal Replacement

Compared to the optimal policy:
- FIFO produces more misses
- FIFO cannot predict future accesses
- FIFO may evict a page that will be used again very soon

FIFO lacks any notion of:
- Future use
- Past use
- Page importance

---

### Belady’s Anomaly (Very Important)

FIFO can exhibit **Belady’s Anomaly**:

> Increasing the number of page frames can **increase** the number of page faults.

This behavior is:
- Counterintuitive
- Undesirable
- A known weakness of FIFO

Belady’s Anomaly demonstrates that:
- FIFO does not behave monotonically with cache size
- More memory does not always mean better performance under FIFO

---

### Stack Property (Context)

Some policies (such as LRU) have a **stack property**:
- The set of pages in a cache of size N is always a subset of those in size N+1

FIFO does **not** have this property, which explains why:
- Belady’s Anomaly can occur
- Performance may worsen when cache size increases

---

### Exam-Relevant Points

You should be able to explain:
- How FIFO replacement works
- Why FIFO is simple but naive
- Why FIFO may evict frequently-used pages
- What Belady’s Anomaly is
- Why FIFO can perform worse with more memory

You should be able to **trace FIFO manually** on a page-reference sequence.

---

### One-Sentence Takeaway (Exam-Ready)

FIFO replaces the page that has been in memory the longest, making it simple to implement but often inefficient, and it can even suffer from Belady’s Anomaly where more memory leads to more page faults.

---
---

## 22.4 Another Simple Policy: Random

This section introduces **Random page replacement**, a simple policy that evicts a randomly chosen page whenever memory pressure requires a replacement.

---

### Basic Idea of Random Replacement

Random replacement works as follows:
- When a page must be evicted from memory
- The operating system selects **one of the resident pages at random**
- The selected page is evicted, regardless of its history or importance

The policy does **not** consider:
- How long a page has been in memory
- How recently or frequently a page has been accessed
- Whether a page will be needed again soon

---

### Why Random Is Considered

Random replacement is interesting because:
- It is **extremely simple to implement**
- It requires **no bookkeeping or metadata**
- It avoids systematic bias toward certain pages

It serves as a **baseline policy** for comparison with more intelligent strategies.

---

### Performance Characteristics

Random replacement:
- Often performs **slightly better than FIFO**
- Performs **worse than the optimal policy**
- Has **high variability** in performance

Its behavior depends entirely on:
- Which pages are randomly selected for eviction

As a result:
- Sometimes it behaves close to optimal
- Sometimes it behaves very poorly
- There is no guaranteed outcome

---

### Experimental Insight from the Text

When the same page-reference sequence is run many times:
- Each run uses a different random seed
- Each run produces a different number of hits

This shows that:
- Random does not have a single predictable hit rate
- Its performance is best described as a **distribution of outcomes**

---

### Comparison with FIFO

Compared to FIFO:
- Random does not always evict the oldest page
- It sometimes keeps frequently-used pages by chance
- It often avoids the worst pathological behaviors of FIFO

Because of this:
- Random can outperform FIFO on many workloads
- FIFO’s weaknesses are systematic, while Random’s are accidental

---

### Limitations of Random Replacement

Despite its simplicity:
- Random does not exploit locality of reference
- It provides no performance guarantees
- Its unpredictable behavior makes it unsuitable for real systems

Modern operating systems prefer policies that:
- Use access history
- Provide stable and predictable performance

---

### Exam-Relevant Points

You should be able to explain:
- How Random replacement works
- Why it is simple to implement
- Why its performance varies widely
- Why it can outperform FIFO in some cases
- Why it is still inferior to history-based policies

---

### One-Sentence Takeaway (Exam-Ready)

Random replacement evicts a randomly chosen page, making it simple and sometimes better than FIFO, but its performance is unpredictable because it relies entirely on chance.

---
---

## 22.5 Using History: LRU — Explanation (Exam-Oriented)

### Why FIFO and Random are problematic
FIFO and Random replacement policies do not consider **how programs actually use memory**.

- **FIFO** evicts the page that entered memory first, even if it is heavily used.
- **Random** evicts an arbitrary page, sometimes working by luck, often not.

In exam terms:
> FIFO and Random are **blind policies** — they ignore page importance.

---

### Core idea behind LRU
The **Least-Recently-Used (LRU)** policy is based on a simple but powerful observation:

> **If a page was used recently, it is likely to be used again soon.**

This is not an assumption pulled from nowhere — it comes from observing **real program behavior**.

---

### Principle of Locality (EXAM-CRITICAL)
LRU relies on the **principle of locality**, which has two forms:

#### 1. Temporal Locality
- Pages accessed **recently** are likely to be accessed **again soon**.
- This is the **main assumption behind LRU**.

#### 2. Spatial Locality
- If page `P` is accessed, nearby pages (`P+1`, `P-1`) are likely to be accessed.
- Explains why paging and caches work well together.

⚠️ Important exam note:  
Locality is a **heuristic**, not a guarantee. Some programs do not exhibit locality.

---

### How LRU works (mechanism)
When a page fault occurs and memory is full:

1. Examine all pages currently in memory
2. Determine which page was accessed **least recently**
3. Evict that page

LRU uses **history (recency)**, not arrival time or randomness.

---

### Why LRU performs well
- **Optimal** replaces the page used **furthest in the future**
- **LRU** replaces the page used **furthest in the past**

Because real programs tend to repeat access patterns:
> **The recent past is often a good predictor of the near future**

This is why LRU often **approaches optimal performance**, unlike FIFO or Random.

---

### Comparison of replacement policies

| Policy  | Uses History | Exploits Locality | Can approach Optimal |
|--------|--------------|-------------------|----------------------|
| FIFO   | No           | No                | No                   |
| Random | No           | No                | No                   |
| LRU    | Yes (recency)| Yes (temporal)    | Yes (often)          |

---

### Related policies (do not confuse in exams)
- **LRU**: Least recently used (good)
- **LFU**: Least frequently used (sometimes misleading)
- **MRU**: Most recently used (usually poor)
- **MFU**: Most frequently used (usually poor)

Policies that **ignore locality** generally perform poorly.

---

### Typical exam questions on LRU
1. **Trace problems**
   - Given a reference string and number of frames
   - Count hits and misses using LRU

2. **Conceptual**
   - Why does LRU outperform FIFO?
   - Which locality does LRU exploit? → **Temporal locality**

3. **Comparison**
   - Which policy avoids Belady’s anomaly? → **LRU**
   - Which policy has the stack property? → **LRU**

---

### One-sentence takeaway (exam-ready)
**LRU replaces the page that has not been used for the longest time, exploiting temporal locality to approximate the optimal replacement policy.**

---
---

## 22.6 Workload Examples

This section studies **how page-replacement policies behave under different memory access patterns**.  
The key lesson is that **there is no universally best policy**: performance depends heavily on the **workload**.

---

## What is a Workload?
A **workload** is the sequence of virtual page references generated by a program.

Different workloads:
- exhibit different locality properties
- stress replacement policies in different ways

---

## Workload 1: No-Locality Workload

### Description
- Each memory reference is to a **random page**
- No temporal locality
- No spatial locality
- 100 unique pages
- 10,000 total references
- Cache size varies from 1 to 100 pages

### Observed Behavior
- FIFO, Random, and LRU perform **almost identically**
- Hit rate is determined **only by cache size**
- When cache size ≥ number of unique pages → **100% hit rate**
- Otherwise, misses are unavoidable

### Key Insight
- Without locality, **history-based policies provide no benefit**

### Exam Takeaway
**If a workload has no locality, sophisticated replacement policies do not outperform simple ones.**

---

## Workload 2: The 80–20 Workload (Hot–Cold Pages)

### Description
- 80% of memory references go to 20% of pages (**hot pages**)
- Remaining 20% of references go to the other 80% of pages (**cold pages**)
- 100 unique pages total
- Strong **temporal locality**

### Observed Behavior
- LRU significantly outperforms FIFO and Random
- LRU keeps hot pages resident in memory
- FIFO and Random may evict hot pages
- Optimal performs best due to future knowledge

### Key Insight
- Locality allows LRU to use history effectively

### Exam Takeaway
**LRU performs well when workloads exhibit strong temporal locality.**

---

## Workload 3: Looping-Sequential Workload

### Description
- Pages accessed in strict sequence: 0, 1, 2, ..., 49
- Sequence repeats continuously
- Cache size slightly **smaller than the loop length**

### Observed Behavior
- FIFO and LRU can experience **0% hit rate**
- Pages are evicted just before being reused
- Random performs better by occasionally retaining useful pages
- Optimal still performs best

### Key Insight
- This workload is a **pathological case** for LRU and FIFO

### Exam Takeaway
**LRU is not universally optimal and can fail badly on looping sequential access patterns.**

---

## Comparative Summary

| Workload Type           | FIFO | Random | LRU | Optimal |
|------------------------|------|--------|-----|---------|
| No locality            | Same | Same   | Same| Best    |
| 80–20 (high locality)  | Poor | Poor   | Good| Best    |
| Looping sequential     | Poor | Better | Poor| Best    |

---

## Core Conceptual Lessons (Exam-Critical)

- Replacement policy performance depends on **workload**
- **Locality** determines whether history-based policies help
- LRU:
  - Excellent with locality
  - Vulnerable to adversarial patterns
- Random:
  - Generally inferior
  - Sometimes avoids worst-case behavior
- Optimal:
  - Theoretical benchmark only

---

## One-Sentence Takeaway (Exam-Ready)
**Page-replacement policies must be evaluated in the context of workload locality, as policies like LRU excel with locality but can perform poorly on certain access patterns.**

---
---

## 22.7 Implementing Historical Algorithms

This section explains **why history-based policies (especially LRU) are hard to implement efficiently**, even though they perform well conceptually.

---

## Why Historical Policies Are Better (in Theory)

- Policies like **LRU** outperform FIFO and Random because they:
  - avoid evicting recently used (important) pages
  - exploit **temporal locality**
- However, better decisions come at a **higher implementation cost**

---

## The Core Implementation Problem

### What perfect LRU requires
To implement **true LRU**, the system must:

- Update recency information **on every memory reference**
  - instruction fetch
  - load
  - store
- Move the accessed page to the **most-recently-used (MRU)** position
- Know at replacement time which page is the **least-recently-used**

This means **accounting work on every memory access**, which is extremely frequent.

---

## Comparison with FIFO (Why FIFO Is Cheaper)

| Policy | When data structures are updated |
|------|----------------------------------|
| FIFO | Only on page insert or eviction |
| LRU  | On **every memory reference** |

👉 This is why FIFO is cheap and LRU is expensive.

---

## Hardware-Assisted LRU (Theoretical Approach)

One possible solution is **hardware support**:

- On every page access:
  - hardware writes a timestamp (or counter) for that page
- At replacement time:
  - OS scans all pages
  - evicts the page with the **oldest timestamp**

### Why this still fails
- Modern systems have **millions of pages**
- Scanning all pages to find the oldest one is **prohibitively slow**
- Example:
  - 4 GB memory
  - 4 KB pages
  - ≈ 1 million pages to scan

---

## The Key Question (Exam-Critical)

> Do we really need the **exact** least-recently-used page?

This motivates the next idea:

- **Approximate LRU**
- Trade perfect accuracy for **much better performance**

---

## Big Insight

- Perfect LRU is:
  - conceptually simple
  - practically expensive
- Real systems must use:
  - **approximations**
  - limited hardware support
  - heuristics that capture locality without full tracking

---

## One-Sentence Takeaway (Exam-Ready)

**Although LRU performs well by exploiting history, implementing perfect LRU is too expensive in practice, motivating the use of efficient approximations.**

---
---

## 22.8 Approximating LRU

Perfect LRU is too expensive to implement in real systems.  
This section explains **how operating systems approximate LRU efficiently**, using limited hardware support, while preserving most of LRU’s benefits.

---

## Why Approximate LRU?

### Problem with Perfect LRU
- Perfect LRU requires updating data structures **on every memory reference**
- Replacement requires finding the **exact least-recently-used page**
- With millions of pages, this becomes **computationally prohibitive**

### Core Question
**Do we really need the exact LRU page, or is a good approximation enough?**

Answer: an approximation is sufficient — and widely used.

---

## Hardware Support: The Use (Reference) Bit

### Use Bit Basics
- Each page has a **use bit** (also called **reference bit**)
- Stored in:
  - page table entries, or
  - a separate OS-managed array
- Behavior:
  - When a page is **read or written**, hardware sets the use bit to `1`
  - Hardware **never clears** the bit
  - Clearing is done by the **OS**

This provides **cheap, coarse-grained history information**.

---

## The Clock Algorithm (Second-Chance Replacement)

### High-Level Idea
- Pages are arranged in a **circular list**
- A pointer (the “clock hand”) moves through pages
- Replacement proceeds by examining use bits

### Replacement Procedure
1. Inspect the page pointed to by the clock hand
2. If its use bit is `1`:
   - Clear the use bit (`1 → 0`)
   - Move the clock hand to the next page
3. If its use bit is `0`:
   - Select this page for eviction
4. Repeat until a victim page is found

### Interpretation
- Pages recently used get a **second chance**
- Pages not used recently are eventually evicted

---

## Why Clock Works Well

- Avoids scanning all pages at once
- Approximates LRU using **recent usage**
- Scales well to large memories
- Captures most of the benefit of LRU at much lower cost

---

## Performance Characteristics

From workload experiments:
- Clock performs:
  - Worse than perfect LRU
  - Much better than FIFO and Random
- Especially effective for workloads with **temporal locality**
- Suffers less overhead than full LRU

---

## Important Clarifications

- Clock is **not the only** LRU approximation
- Any algorithm that:
  - periodically clears use bits, and
  - prefers pages with use bit `0`
  is an LRU approximation
- Clock’s advantage is **simplicity and efficiency**

---

## Comparison Summary

| Policy | History Used | Implementation Cost | Practical Use |
|------|-------------|---------------------|---------------|
| FIFO | No | Very low | Rare today |
| Random | No | Very low | Sometimes |
| LRU (perfect) | Yes (exact) | Too high | Impractical |
| Clock | Yes (approx.) | Low | Widely used |

---

## One-Sentence Takeaway (Exam-Ready)

**Approximating LRU using a hardware-maintained use bit and the clock algorithm provides most of LRU’s benefit at a fraction of the implementation cost.**

---
---

## 22.9 Considering Dirty Pages

This section refines page-replacement decisions by considering **whether a page has been modified while in memory**.

---

## Dirty vs. Clean Pages

- **Dirty page**: a page that has been **modified (written to)** while in memory  
- **Clean page**: a page that has **not been modified**

### Why this matters
- Evicting a **dirty page** requires:
  - writing it back to disk (expensive I/O)
- Evicting a **clean page**:
  - no disk write needed
  - physical frame can be reused immediately

👉 Therefore, **evicting clean pages is cheaper than evicting dirty pages**.

---

## Hardware Support: The Modified (Dirty) Bit

- Hardware maintains a **modified bit** (also called **dirty bit**) per page
- Behavior:
  - Set to `1` when a page is written to
  - Remains `0` if the page is only read
- Stored in:
  - page table entries (commonly)

This bit allows the OS to distinguish **clean vs. dirty pages** during replacement.

---

## Integrating Dirty Pages into Replacement

### Motivation
Replacement policies should consider **both**:
- how recently a page was used
- whether evicting it would require disk I/O

---

## Clock Algorithm with Dirty Pages (Conceptual)

The clock algorithm can be extended to consider two bits:
- **Use bit** (recently used?)
- **Dirty bit** (modified?)

### Example eviction preference order
1. Pages that are **unused and clean**
2. Pages that are **unused but dirty**
3. Pages that are **used and clean**
4. Pages that are **used and dirty**

This ordering:
- minimizes disk writes
- preserves recently used pages when possible

---

## Key Insight

- Dirty pages are more expensive to evict
- Replacement policies that prefer clean pages can **significantly reduce I/O cost**
- This optimization does not change correctness, only **performance**

---

## Exam-Relevant Points

- Dirty bit is **set on writes**, not reads
- Evicting a dirty page implies a **write-back to disk**
- Clean pages can be evicted **without disk I/O**
- Modern VM systems almost always consider dirty pages

---

## One-Sentence Takeaway (Exam-Ready)

**By preferring clean pages over dirty ones during replacement, the OS reduces costly disk writes while maintaining correct virtual memory behavior.**

---
---

## 22.10 Other VM Policies

Page replacement is the most visible policy in a virtual memory system, but it is **not the only one**.  
The VM subsystem also includes policies that decide **when pages are brought into memory** and **how pages are written back to disk**.

---

## Page Selection Policy (When to Bring Pages In)

Beyond deciding **which page to evict**, the OS must decide **when to load a page into memory**.  
This decision is known as the **page selection policy**.

### Demand Paging (Default Behavior)
- The OS loads a page **only when it is accessed**
- Pages are brought into memory **on demand**
- Advantages:
  - Avoids loading unused pages
  - Reduces unnecessary I/O
- This is the **standard approach** used by most systems

---

## Prefetching (Speculative Loading)

Instead of waiting for a page to be accessed, the OS may **guess** that a page will be needed soon and load it early.

### Example
- If code page `P` is accessed
- The OS assumes page `P + 1` will soon be accessed
- Page `P + 1` is prefetched into memory

### Key Points
- Prefetching can improve performance
- But it is risky:
  - Wrong guesses waste memory and I/O
- Should only be used when there is a **reasonable chance of success**
- Relies heavily on **spatial locality**

---

## Write Policy (How Pages Are Written to Disk)

Another VM policy concerns **how modified pages are written back to disk**.

### Simple Approach
- Write pages to disk **one at a time**

### Optimized Approach: Clustering (Grouping Writes)
- Collect multiple dirty pages in memory
- Write them to disk **together in one large I/O operation**

### Why Clustering Works
- Disk drives are much more efficient at:
  - one large write
  - than many small writes
- Reduces seek and rotational overhead

---

## Key Insights

- VM performance depends on **multiple interacting policies**
- Replacement decides *what leaves*
- Selection decides *what enters*
- Write policy decides *how data goes back to disk*
- These policies exploit **locality** and **disk behavior**

---

## Exam-Relevant Takeaways

- Demand paging = load pages **only when accessed**
- Prefetching = speculative loading based on locality
- Clustering = grouping disk writes for efficiency
- Page replacement is important, but **not sufficient alone**

---

## One-Sentence Takeaway (Exam-Ready)

**Virtual memory performance depends not only on page replacement, but also on policies for page selection (demand paging vs. prefetching) and efficient write-back to disk (clustering).**


---
---

## 22.11 Thrashing

This section discusses what happens when **memory demand exceeds physical memory capacity**, and how operating systems attempt to cope with this situation.

---

## What Is Thrashing?

**Thrashing** occurs when:
- Physical memory is **oversubscribed**
- The combined memory needs of running processes **exceed available RAM**
- The system spends most of its time **paging** (swapping pages in and out)
- Very little useful work is done

In thrashing:
- CPU utilization drops
- Disk activity increases dramatically
- Performance collapses

---

## Why Thrashing Happens

- Too many processes are running at once
- Each process has a **working set** (the set of pages it actively uses)
- If the sum of working sets does **not fit in memory**, constant page faults occur

---

## The Working Set Concept

- A **working set** is the collection of pages a process is actively using
- If a process’s working set fits in memory:
  - It can make progress
- If not:
  - It will continuously fault

Thrashing is fundamentally a **working-set mismatch problem**.

---

## Admission Control (Classic Solution)

### Idea
Instead of running all processes at once:
- Run **fewer processes**
- Ensure their combined working sets fit in memory

### How It Works
- The OS temporarily **suspends or delays** some processes
- The remaining processes run efficiently
- Overall system throughput improves

### Philosophy
> It is better to do **less work well** than **more work poorly**

This approach is known as **admission control**.

---

## Modern OS Approach: Out-of-Memory Killer

Some modern systems (e.g., Linux) take a more aggressive approach.

### Out-of-Memory (OOM) Killer
- Activated when memory is critically low
- Selects a **memory-intensive process**
- Terminates it to free memory immediately

### Advantages
- Quickly relieves memory pressure
- Simple and effective

### Disadvantages
- May kill important processes
- Can lead to poor user experience
  - e.g., killing the display server

---

## Key Insights

- Thrashing is a system-wide performance failure
- It cannot be fixed by page replacement alone
- Requires **global memory management decisions**
- Balancing multiprogramming level is essential

---

## Exam-Relevant Points

- Thrashing = excessive paging + little useful work
- Caused by working sets not fitting in memory
- Admission control reduces the number of active processes
- OOM killer is a last-resort mechanism

---

## One-Sentence Takeaway (Exam-Ready)

**Thrashing occurs when the combined working sets of running processes exceed physical memory, causing constant paging and severe performance degradation.**

---
---

# Chapter 22 — Beyond Physical Memory: Policies

This chapter focuses on **how the operating system makes decisions under memory pressure**.  
While previous chapters explained *how* paging works (mechanisms), this chapter explains *which pages should be kept* and *which should be evicted* when memory is full.

---

## 1. Main Memory as a Cache

A fundamental idea of this chapter is that:

- **Main memory acts as a cache for disk**
- Disk is:
  - very large
  - extremely slow
- Memory is:
  - small
  - fast

Thus, virtual memory management is essentially a **cache management problem**.

The OS aims to:
- **maximize cache hits**
- **minimize cache misses**

---

## 2. Why Replacement Policies Are Critical

Disk access is **orders of magnitude slower** than memory access.

Even a very small miss rate:
- dominates overall execution time
- severely degrades performance

This is captured by **Average Memory Access Time (AMAT)**:
- a tiny increase in miss rate causes a huge increase in AMAT

**Key insight:**  
Replacement policy quality has a **direct and dramatic impact** on system performance.

---

## 3. Replacement Policies Overview

### Optimal Replacement (Theoretical Baseline)

- Replaces the page that will be accessed **furthest in the future**
- Produces the **fewest possible cache misses**
- Impossible to implement in practice (requires future knowledge)

Used only as:
- a comparison baseline
- a way to measure how close real policies are to ideal behavior

---

### FIFO (First-In, First-Out)

- Evicts the page that entered memory first
- Very simple to implement
- Ignores:
  - how often a page is used
  - how recently a page is used

Problems:
- Can evict heavily used pages
- Suffers from **Belady’s anomaly**
- Does not exploit locality

---

### Random Replacement

- Evicts a randomly chosen page
- Simple and sometimes surprisingly effective
- Avoids some worst-case behaviors of FIFO

Limitations:
- Ignores locality
- Performance depends on luck

---

### LRU (Least Recently Used)

- Evicts the page that has not been used for the longest time
- Exploits **temporal locality**
- Often performs close to optimal

Key assumption:
> Pages used recently are likely to be used again soon.

Limitations:
- Perfect LRU is expensive to implement

---

## 4. Locality: The Foundation of Good Policies

Programs tend to exhibit **locality of reference**:

### Temporal Locality
- Recently accessed pages are likely to be accessed again

### Spatial Locality
- Pages near a recently accessed page are likely to be accessed

LRU works well because it exploits temporal locality.  
However, locality is a **heuristic**, not a guarantee.

---

## 5. Workloads Explain Policy Behavior

Replacement policy effectiveness depends heavily on **workload characteristics**.

### No-Locality Workload
- Random access pattern
- FIFO, Random, and LRU perform similarly
- Cache size dominates performance

### 80–20 Workload
- 80% of accesses go to 20% of pages
- Strong temporal locality
- LRU significantly outperforms FIFO and Random

### Looping-Sequential Workload
- Sequential access that loops
- FIFO and LRU can achieve 0% hit rate
- Random may perform better by chance

**Key lesson:**  
No replacement policy is universally optimal.

---

## 6. Implementing Historical Policies

Perfect LRU requires:
- updating metadata on every memory access
- tracking exact recency for all pages

This is:
- computationally expensive
- not scalable for modern systems

---

## 7. Approximating LRU

To make LRU practical, systems use **approximations**.

### Use (Reference) Bit
- Set by hardware on page access
- Cleared by the OS
- Provides coarse history information

### Clock (Second-Chance) Algorithm
- Pages arranged in a circular list
- Pages with use bit = 1 get a second chance
- Pages with use bit = 0 are evicted

Clock:
- approximates LRU
- scales well
- is widely used in real systems

---

## 8. Considering Dirty Pages

Eviction cost differs between pages:

- **Clean page**: can be evicted without disk I/O
- **Dirty page**: must be written back to disk

Hardware tracks this using a **dirty (modified) bit**.

Replacement policies often:
- prefer evicting clean pages
- reduce costly disk writes

---

## 9. Other Virtual Memory Policies

Replacement is not the only VM policy.

### Page Selection Policy
- **Demand paging**: load pages only when accessed
- **Prefetching**: speculate and load pages early

### Write Policy
- Writing pages individually is inefficient
- **Clustering** groups writes to reduce disk overhead

---

## 10. Thrashing

### What Is Thrashing?
Thrashing occurs when:
- physical memory is oversubscribed
- processes’ working sets do not fit in memory
- system spends most time paging

Effects:
- CPU utilization drops
- Disk activity spikes
- System performance collapses

---

### Coping with Thrashing

#### Admission Control
- Reduce number of running processes
- Ensure working sets fit in memory
- Improves throughput

#### Out-of-Memory (OOM) Killer
- Terminates memory-intensive processes
- Fast but potentially disruptive

---

## 11. Final Big Picture

- Main memory is a cache for disk
- Disk misses dominate performance
- Locality enables effective caching
- LRU exploits locality but is imperfect
- Real systems rely on approximations
- Replacement policy must match workload
- Oversubscription leads to thrashing

---

## One-Sentence Exam Takeaway

**Virtual memory performance depends on intelligent page-replacement policies that exploit locality, approximate history efficiently, and avoid system-wide thrashing under memory pressure.**


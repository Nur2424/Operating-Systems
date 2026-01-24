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

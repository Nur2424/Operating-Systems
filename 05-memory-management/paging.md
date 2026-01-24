## Chapter 18 — Paging: Introduction (Chapter Overview)

This section introduces the motivation for paging and explains why fixed-size memory management is preferred over variable-sized approaches.

---

### Two Approaches to Space Management

Operating systems generally manage memory using one of two strategies:

#### 1. Variable-Sized Pieces
- Used in:
  - Contiguous memory allocation
  - Segmentation
- Memory is divided into chunks of different sizes

**Problems:**
- Fragmentation occurs over time
  - Internal fragmentation (base & bounds)
  - External fragmentation (segmentation)
- Memory allocation becomes increasingly difficult

These issues are directly tested in exams through fragmentation and allocation questions.

---

#### 2. Fixed-Sized Pieces (Paging)
- Memory is divided into equal-sized units

In virtual memory:
- **Pages**: fixed-size blocks of virtual memory
- **Page frames**: fixed-size blocks of physical memory
- All pages and frames have the same size

To lock it in:

	•	Page → fixed-size block of virtual memory
	•	Frame (page frame) → fixed-size block of physical memory

This approach is called **paging**.

---

### Why Paging Is Introduced

Paging is designed to eliminate key problems of variable-sized allocation:

- Eliminates **external fragmentation**
- Simplifies memory allocation
- Any free frame can hold any page

Paging removes the need for best-fit or worst-fit allocation strategies.

---

### Conceptual Shift from Base-and-Bounds

- Base-and-bounds requires contiguous physical memory
- Paging allows a process to be spread across non-contiguous frames
- Physical contiguity is no longer required

---

### Preparation for Later Sections

This introduction sets the foundation for:
- Page number + offset address structure
- Page tables and address translation
- Performance costs of paging
- Page faults and replacement policies

These topics are heavily tested in exams and are covered in later sections.

---

### Key Takeaway

Paging virtualizes memory by dividing it into fixed-size blocks, avoiding fragmentation issues inherent in variable-sized memory allocation.

---
---

## 18.1 A Simple Example and Overview

This section explains **how paging performs address translation**, using a small concrete example.  
It introduces all the mechanics later tested in paging-related exam questions.

---

### Example Setup

#### Virtual Address Space
- Total size: **64 bytes**
- Page size: **16 bytes**
- Number of virtual pages: 64 / 16 = 4

- Virtual pages: **VP 0, 1, 2, 3**

This directly maps to exam questions asking:
- number of pages
- page number bits

---

#### Physical Memory
- Total size: **128 bytes**
- Frame size: **16 bytes**
- Number of physical frames: 128 / 16 = 8

- Frames are numbered: **0–7**

Key idea:
- Virtual pages and physical frames are independent
- Pages do **not** need to be contiguous in physical memory

---

### Non-Contiguous Page Placement

In the example, the OS places pages as follows:

| Virtual Page (VPN) | Physical Frame (PFN) |
|--------------------|----------------------|
| 0                  | 3                    |
| 1                  | 7                    |
| 2                  | 5                    |
| 3                  | 2                    |

This illustrates:
- Paging removes the need for contiguous allocation
- External fragmentation is eliminated

---

### Page Table

To track page placement, the OS maintains a **page table**.

Properties:
- Per-process data structure
- Indexed by **Virtual Page Number (VPN)**
- Stores **Physical Frame Number (PFN)**

For this example, the page table has 4 entries:


VPN 0 → PFN 3,
VPN 1 → PFN 7,
VPN 2 → PFN 5,
VPN 3 → PFN 2

Exam relevance:
- Page table size = number of virtual pages
- Page tables are per process

---

### Virtual Address Structure

#### Total bits
- Address space = 64 bytes
- Required bits: log2(64) = 6 bits

#### Bit split
- Page size = 16 bytes → **offset = 4 bits**
- Remaining bits → **VPN = 2 bits**

 VPN (2 bits) | offset (4 bits) 

This split is frequently tested in exams.

---

### Address Translation Algorithm

Given a virtual address:

1. Split virtual address into:
   - VPN
   - offset
2. Use VPN to index the page table
3. Obtain PFN
4. Replace VPN with PFN
5. Keep offset unchanged
6. Form physical address

Important rule:
- **Offset is never translated**
- Only the page number is translated

---

### Worked Translation Example

Given virtual address **21**:

1. Binary representation: 21=010101

2. Split into:
-VPN = 01(1)
-offset = 0101(5)

3. Page table lookup: VPN 1 → PFN 7

4. Physical address: PFN || offset = 1110101

5. Decimal physical address: 117

---

### Comparison with Base-and-Bounds (Chapter 15)

**Base & Bounds** 
physical_address = base + virtual_address 

** Paging** 
physical_address = PFN || offset

Paging:
- Removes external fragmentation
- Allows non-contiguous placement
- Requires more complex translation

---

### Common Exam Pitfalls

- Translating the offset (incorrect)
- Confusing VPN with PFN
- Assuming physical contiguity
- Incorrect bit split
- Forgetting page tables are per process

---

### Key Takeaway

Paging translates virtual addresses by splitting them into a **page number and offset**, using a page table to map pages to frames, while keeping the offset unchanged.

---
---

## 18.2 Where Are Page Tables Stored?

This section explains **why page tables cannot be stored inside the MMU** and must instead be kept in **main memory**, and analyzes the memory overhead caused by page tables.

---

### Why Page Tables Are Large

With paging, each process needs a translation entry for **every virtual page**.

#### Example: 32-bit Virtual Address Space with 4 KB Pages

- Virtual address size: **32 bits**
- Page size: **4 KB = 2¹² bytes**
  - Offset size = **12 bits**
- Remaining bits are used for the Virtual Page Number (VPN): VPN bits = 32 − 12 = 20

Number of virtual pages per process: 2²⁰ = 1,048,576 pages 

---

### Page Table Size Calculation

Assuming:
- One page table entry (PTE) per virtual page
- PTE size = **4 bytes**

Page table size per process: 2²⁰ × 4 bytes = 4 MB 

If the system runs 100 processes: 100 × 4 MB = 400 MB

This is a significant memory overhead used **only for address translation**.

---

### Where Page Tables Are Stored

Because page tables are so large:
- They cannot be stored in on-chip MMU registers
- They are stored in **main memory**

Specifically:
- Each process has its own page table
- Page tables are managed by the operating system
- At this stage, assume page tables reside in **physical memory**

(Future chapters discuss virtualizing OS memory and swapping page tables, but this is ignored here.)

---

### Key Properties of Page Tables

- Page tables are **per-process**
- Page table size depends on:
  - Virtual address space size
  - Page size
  - Page table entry (PTE) size
- Page table size is based on **virtual pages**, not physical frames

---

### Exam-Relevant Pitfalls

- Forgetting to subtract offset bits when computing VPN bits
- Confusing number of virtual pages with number of physical frames
- Forgetting to multiply by PTE size when asked for memory usage
- Assuming page tables are stored in the MMU

---

### Connection to Next Sections

Because page tables are stored in memory:
- Address translation may require extra memory accesses
- This leads to performance issues
- Motivates later topics such as paging being “too slow” and the use of TLBs

---

### Key Takeaway

Page tables are too large to be stored in hardware, so the OS stores them in main memory, trading simplicity and flexibility for increased memory usage and translation cost.

---
---

## 18.3 What’s Actually In The Page Table?

This section explains **what information is stored inside a page table entry (PTE)** and why each field exists. These bits are essential for **address translation, protection, page faults, and page replacement**, all of which appear in exams.

---

### Page Table Organization

- A page table maps: Virtual Page Number (VPN) → Physical Frame Number (PFN)

- In this chapter, we assume a **linear page table**:
- Implemented as an array
- Indexed directly by the VPN
- Each array entry is a **Page Table Entry (PTE)**

Important:
- Page table size depends on the **number of virtual pages**
- It does **not** depend on the number of physical frames

---

### Contents of a Page Table Entry (PTE)

A PTE contains more than just the PFN. It includes multiple **status and control bits** used by hardware and the OS.

---

### Valid Bit

**Purpose**
- Indicates whether the virtual page is part of the process’s address space

**Meaning**
- `valid = 1` → page belongs to the process
- `valid = 0` → page is unused / illegal

**Why it matters**
- Supports **sparse address spaces**
- OS does not allocate physical memory for unused virtual pages

**Exam relevance**
- Accessing an invalid page:
- Causes a trap / exception
- OS usually terminates the process

---

### Protection Bits (R / W / X)

**Purpose**
- Specify allowed operations on the page:
- Read
- Write
- Execute

**Behavior**
- If a process violates permissions:
- Hardware raises a trap
- OS intervenes

**Exam relevance**
- Explains memory protection
- Prevents user programs from modifying code or kernel memory

---

### Present Bit

**Purpose**
- Indicates whether the page is currently in physical memory

**Meaning**
- `present = 1` → page is in RAM
- `present = 0` → page is on disk (swapped out)

**Exam relevance**
- Accessing a page with `present = 0` causes a **page fault**
- Fundamental to virtual memory and demand paging

---

### Dirty Bit

**Purpose**
- Indicates whether the page has been modified since being loaded

**Meaning**
- `dirty = 1` → page has been written to
- `dirty = 0` → page unchanged

**Why it matters**
- Dirty pages must be written back to disk on eviction
- Clean pages can be discarded

**Exam relevance**
- Used when reasoning about page replacement cost

---

### Reference (Accessed) Bit

**Purpose**
- Indicates whether the page has been accessed recently

**Behavior**
- Set by hardware on page access

**Why it matters**
- Used by page replacement algorithms:
- LRU (approximate)
- Second Chance / Clock

**Exam relevance**
- Directly tested in Clock / Second Chance questions

---

### Physical Frame Number (PFN)

**Purpose**
- Identifies the physical frame where the page resides

**Role**
- Used during address translation
- Replaces the VPN in the virtual address

---

### Architecture-Specific Bits (Low Priority)

Examples (x86):
- User/Supervisor (U/S)
- Cache-related bits (PWT, PCD, PAT, G)

Exam note:
- Do not memorize architecture-specific details
- Understand that architectures may add extra control bits

---

### Summary of PTE Fields

| Field | Purpose |
|------|---------|
| PFN | Location of the page in physical memory |
| Valid bit | Indicates if page belongs to address space |
| Protection bits | Control read/write/execute permissions |
| Present bit | Indicates if page is in memory or on disk |
| Dirty bit | Tracks whether page was modified |
| Reference bit | Tracks recent usage for replacement |

---

### Common Exam Pitfalls

- Assuming a PTE stores only the PFN
- Confusing **valid bit** with **present bit**
- Forgetting the role of the reference bit
- Treating invalid pages as swapped pages (incorrect)

---

### Key Takeaway

A page table entry combines **address translation information** with **protection and status bits**, enabling safe memory access, virtual memory, and efficient page replacement.

## 18.4 Paging: Also Too Slow

This section explains **why naive paging causes performance problems** when page tables are stored in main memory, and motivates later optimizations.

---

### Core Problem

- Page tables are stored in **main memory**
- Therefore, address translation itself requires **memory accesses**
- Result: **every memory reference becomes more expensive**

---

### Example Instruction movl 21, %eax 

We ignore instruction fetch and consider only the **explicit data access**.

Goal:
- Translate virtual address `21`
- Load data from the correct physical address

---

### Address Translation Cost (Step-by-Step)

Assumptions:
- Page table is a **linear array**
- Page Table Base Register (PTBR) holds the **physical address** of the page table
- Page Table Entries (PTEs) are stored in memory

Steps performed by hardware:

1. Extract **Virtual Page Number (VPN)** from virtual address
2. Compute address of the PTE: PTEAddr = PTBR + (VPN × sizeof(PTE))

3. **Access memory** to fetch the PTE
4. Check PTE:
- valid bit
- protection bits
5. Extract **Physical Frame Number (PFN)**
6. Form physical address: PhysAddr = (PFN << SHIFT) | offset

7. **Access memory again** to fetch the actual data

---

### Key Observation (Critical for Exams)

For **one logical memory access** by the program:

- 1 memory access → fetch the PTE
- 1 memory access → fetch the data

➡️ **2 memory accesses instead of 1**

This applies to:
- Loads
- Stores
- Instruction fetches

---

### Performance Impact

- Paging can slow execution by a factor of **2× or more**
- Extra memory accesses are expensive
- This overhead occurs **on every memory reference**

---

### Why This Matters for Exams

This section explains the motivation behind:
- Effective Access Time (EAT) formulas
- Page-fault probability questions
- Translation Lookaside Buffers (TLBs)

Without understanding this section, EAT and TLB questions become mechanical and error-prone.

---

### Two Fundamental Problems with Paging

Paging introduces two major costs:

1. **Time overhead**
- Extra memory access for translation
2. **Space overhead**
- Large page tables (from Section 18.2)

The rest of the chapter focuses on addressing these problems.

---

### Exam-Relevant Notes

- VPN is used to index the page table
- Offset is **never translated**
- PFN replaces VPN in the physical address
- Bit-level masking and shifting logic explains *why* translation is slow

---

### Common Exam Pitfalls

- Assuming paging adds only one extra access total
- Forgetting instruction fetches also require translation
- Jumping directly to EAT formulas without understanding the source of overhead

---

### Key Takeaway

Naive paging requires an extra memory access for every address translation, making paging too slow without further hardware support.

---
---
## 18.5 A Memory Trace

This section traces **actual memory accesses** generated when paging is used, showing how a simple loop results in many physical memory references. No new mechanisms are introduced; the goal is to **quantify paging overhead**.

---

### Assumptions Used in the Example

- Virtual address space size: **64 KB**
- Page size: **1 KB**
- Page table:
  - Linear (array-based)
  - Stored in physical memory
  - Located at physical address **1 KB**
- Array size: **4000 bytes**
- Code and array reside on different virtual pages

---

### Virtual-to-Physical Page Mappings

Assumed mappings for the example:

| Virtual Page (VPN) | Physical Frame (PFN) | Purpose |
|--------------------|----------------------|---------|
| 1                  | 4                    | Code    |
| 39                 | 7                    | Array   |
| 40                 | 8                    | Array   |
| 41                 | 9                    | Array   |
| 42                 | 10                   | Array   |

---

### Memory Accesses per Loop Iteration

Each loop iteration executes **four instructions** and **one data write**.

#### Instruction Fetches
For each instruction:
- 1 memory access to the page table (translation)
- 1 memory access to fetch the instruction

For 4 instructions: 4 × 2 = 8 memory accesses 

#### Data Access (Array Write)
- 1 page table access to translate data address
- 1 memory access to write data
2 memory accesses

---

### Total Memory Accesses per Iteration 
8 (instruction fetches) + 2 (data access) = 10 memory accesses 

This is per **single loop iteration**.

---

### What the Memory Trace Shows (Figure 18.7)

The trace separates memory accesses into three categories:

1. **Page table accesses**
   - Translation for instructions and data
2. **Code accesses**
   - Instruction fetches
3. **Array accesses**
   - Data writes

Observations:
- Page table accesses dominate
- Same page table entries are reused repeatedly
- Strong locality exists

---

### Why This Matters

This trace explains:
- Why paging is considered “too slow” without optimization
- Why **Effective Access Time (EAT)** is needed
- Why **TLBs** are effective (high locality of translations)
- Why page faults are extremely costly inside loops

---

### Common Exam Pitfalls Clarified

- Assuming one instruction causes one memory access
- Ignoring instruction-fetch translations
- Forgetting page table accesses are real memory accesses
- Underestimating paging overhead in loops

---

### Key Takeaway

With paging, a single logical loop iteration can generate many physical memory accesses due to repeated page-table lookups, even when no page faults occur. 

---
---

# Chapter 18 — Paging: Introduction (Summary)

This chapter introduces **paging** as a fundamental memory virtualization technique and explains its mechanisms, benefits, and inherent costs. Paging is a core topic and heavily tested in exams.

---

## Motivation for Paging

Earlier memory management techniques have major drawbacks:

- **Contiguous allocation / Base & Bounds**
  - Simple and fast
  - Suffers from **internal fragmentation**

- **Segmentation**
  - Logical view of memory
  - Suffers from **external fragmentation**
  - Allocation becomes difficult over time

Paging avoids variable-sized allocation by using **fixed-size blocks**.

---

## Core Idea of Paging

- Virtual memory is divided into **pages**
- Physical memory is divided into **frames**
- Pages and frames are the same size
- Pages can be placed **anywhere** in physical memory

**Key result:**
- Eliminates **external fragmentation**
- Simplifies physical memory allocation

---

## Address Translation with Paging

A virtual address is split into two parts: Virtual Page Number (VPN) | Offset 

Translation steps:
1. VPN indexes the page table
2. Page table entry provides the Physical Frame Number (PFN)
3. PFN replaces VPN
4. Offset is copied unchanged

This VPN/offset split is central to many exam questions.

---

## Page Tables

- Each process has its own **page table**
- Page table maps **VPN → PFN**
- Typically implemented as a **linear array** in this chapter

### Page Table Size

Page table size depends on:
- Virtual address space size
- Page size
- Page Table Entry (PTE) size

Because page tables can be very large, they are stored in **main memory**, not in the MMU.

---

## Contents of a Page Table Entry (PTE)

A PTE contains:

- **PFN** – physical location of the page
- **Valid bit** – whether the page belongs to the address space
- **Protection bits (R/W/X)** – allowed operations
- **Present bit** – whether the page is in memory or on disk
- **Dirty bit** – whether the page was modified
- **Reference (accessed) bit** – tracks recent usage

These fields enable protection, virtual memory, and page replacement.

---

## Why Paging Is Too Slow

Because page tables are stored in memory:

- Each address translation requires a memory access
- Each data or instruction access requires another memory access

Result: 1 logical memory access → 2 physical memory accesses 

This explains:
- Performance overhead of paging
- Need for Effective Access Time (EAT)
- Motivation for TLBs

---

## Memory Trace Insight

A simple loop:
- Generates multiple instruction fetches
- Each fetch requires page table access
- Each data access also requires page table access

Outcome:
- Many physical memory accesses per loop iteration
- Page table accesses dominate

This demonstrates why locality and caching are crucial.

---

## Fundamental Problems Introduced

Paging introduces two major costs:

1. **Space overhead**
   - Large page tables
2. **Time overhead**
   - Extra memory accesses for translation

The remainder of virtual memory design focuses on reducing these costs.

---

## Exam-Oriented Key Takeaways

You should be able to:
- Split addresses into VPN and offset
- Compute page table sizes and memory usage
- Explain what causes page faults
- Describe the purpose of PTE bits
- Explain why paging increases access time

Mastering these points means you understand Chapter 18 at exam level.




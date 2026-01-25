Combine:

	•	Ch 36 — I/O Devices
	•	Ch 37 — Hard disks

Include:

	•	Interrupts
	•	DMA (concept)
	•	Disk scheduling (idea only)

---
---

## 36.1 System Architecture

This section describes **how I/O devices are physically organized and connected to a computer system**, and why a **hierarchical design** is used.

---

## Big Picture

A modern computer system is **not flat**.  
Instead, it is organized as a **hierarchy of buses**, ordered by:

- speed
- cost
- number of devices supported

This design balances **performance, scalability, and cost**.

---

## Core Components

- **CPU**
- **Main Memory**
- **I/O Devices** (graphics cards, disks, keyboards, mice, USB devices, etc.)

These components communicate through different buses.

---

## The Three-Level Bus Hierarchy

### 1. Memory Bus
- Connects **CPU ↔ main memory**
- Characteristics:
  - Very fast
  - Very short
  - Very expensive
  - Supports very few devices
- Purpose:
  - Maximize CPU–memory performance

---

### 2. General I/O Bus (e.g., PCI)
- Connects **high-performance I/O devices**
  - e.g., graphics cards
- Characteristics:
  - Slower than memory bus
  - More flexible
  - Can support multiple devices
- Acts as a bridge between fast core components and slower peripherals

---

### 3. Peripheral I/O Bus (e.g., SATA, SCSI, USB)
- Connects **slow I/O devices**
  - disks
  - keyboards
  - mice
  - USB devices
- Characteristics:
  - Much slower
  - Cheaper
  - Supports many devices
- Designed for scalability rather than speed

---

## Why a Hierarchical Architecture Is Necessary

### Physics Constraints
- Faster buses must be:
  - shorter
  - simpler
  - tightly controlled
- Long wires and many devices increase:
  - latency
  - signal interference

---

### Cost Constraints
- High-speed buses are expensive to design and manufacture
- Using them for all devices would be impractical

---

## Placement of Devices

- **High-performance components** (CPU, memory, GPU):
  - placed close to the CPU
  - connected via fast buses
- **Slow devices** (disks, input devices):
  - placed farther away
  - connected via slower peripheral buses

This prevents slow devices from degrading CPU performance.

---

## Operating System Implications

Because of this architecture:

- The OS must handle:
  - devices with widely different speeds
  - asynchronous I/O
- The CPU cannot wait synchronously for slow devices
- This motivates:
  - interrupts
  - DMA
  - device drivers
  (covered in later sections)

---

## Common Exam Traps

- ❌ All devices connect directly to the CPU  
- ❌ Fast buses are used to connect many slow devices  
- ✅ Faster bus → fewer devices, higher cost  
- ✅ Slower bus → many devices, lower cost  

---

## One-Sentence Takeaway (Exam-Ready)

**Modern systems use a hierarchical bus architecture so that fast, expensive buses serve only high-performance components, while slower, cheaper buses connect many low-speed I/O devices without harming CPU–memory performance.**

---
---

## 36.2 A Canonical Device

This section introduces a **canonical (idealized) I/O device** to explain the common structure shared by all real devices.

---

### Purpose of the Canonical Device
- Not a real device
- Used as a **conceptual model**
- Helps explain:
  - how the OS controls devices
  - what hardware must expose
  - how device complexity is hidden

---

### Two Fundamental Parts of Any I/O Device

#### 1. Hardware Interface (Visible to the OS)
- The **only part** the OS can see and use
- Acts like a hardware API
- Allows the OS to:
  - control the device
  - query its state
  - send and receive data
- Defined by:
  - registers
  - a protocol for accessing them
- Fixed and documented

---

#### 2. Internal Structure (Hidden from the OS)
- Implementation-specific
- Responsible for actually doing the work
- Can include:
  - simple logic or chips (simple devices)
  - a micro-controller (embedded CPU)
  - device-local memory (SRAM / DRAM)
  - other hardware-specific components

---

### Firmware
- Many modern devices contain **firmware**
- Firmware = software running *inside* the device
- Can be large and complex
- Example:
  - RAID controllers may include hundreds of thousands of lines of firmware
- Still hidden from the OS

---

### Key Design Principle
- The OS **does not care** how the device works internally
- The OS depends **only on the interface**
- This separation enables:
  - portability
  - modularity
  - independent device evolution

---

### One-Sentence Takeaway (Exam-Ready)
**An I/O device consists of a hardware interface exposed to the OS and an internal implementation (often including firmware) that realizes the device’s functionality.**

---

---

## 36.3 The Canonical Protocol

This section explains **how the OS interacts with a device** using the device interface.

---

### Device Interface Registers

A canonical device exposes three registers:

#### Status Register
- Read-only
- Indicates device state:
  - busy
  - ready
  - finished
  - error

#### Command Register
- Written by the OS
- Specifies the operation the device should perform

#### Data Register
- Used to:
  - send data to the device
  - receive data from the device

---

### The Canonical I/O Interaction Protocol

A typical OS–device interaction follows four steps:

1. **Wait until device is ready**
   - OS repeatedly reads the status register
   - This is called **polling**

2. **Transfer data**
   - OS writes data to the data register
   - Large transfers may require multiple writes

3. **Issue command**
   - OS writes to the command register
   - Signals the device to begin execution

4. **Wait for completion**
   - OS polls the status register again
   - Device reports success or failure

---

### Programmed I/O (PIO)
- CPU performs the data movement itself
- CPU is actively involved during the entire operation
- This protocol is an example of **programmed I/O**

---

### Major Limitation: Inefficiency
- Polling wastes CPU cycles
- CPU spins while waiting for slow devices
- CPU cannot run other ready processes
- Especially bad for:
  - disks
  - network devices

This inefficiency motivates:
- interrupts
- DMA (discussed later)

---

### Common Exam Traps
- Polling is efficient for slow devices ❌
- CPU can do useful work while polling ❌
- Polling wastes CPU time ✅
- PIO ties CPU directly to data movement ✅

---

### One-Sentence Takeaway (Exam-Ready)
**The canonical I/O protocol uses polling and programmed I/O via status, command, and data registers, but wastes CPU time waiting for slow devices.**

---
---

## 36.4 Lowering CPU Overhead With Interrupts

This section introduces **interrupts** as a mechanism to reduce CPU waste caused by polling during I/O operations.

---

## The Problem With Polling (Recap)

- With polling (PIO):
  - CPU repeatedly checks device status
  - CPU spins while device works
  - Wastes CPU cycles
- Especially inefficient for **slow devices** (e.g., disks)

---

## Core Idea: Interrupts

Instead of polling:

1. OS issues an I/O request
2. The calling process is **put to sleep**
3. OS **context-switches** to another ready process
4. When the device finishes, it raises a **hardware interrupt**
5. CPU jumps to an **interrupt handler (ISR)**
6. OS completes the request and **wakes the sleeping process**

---

## Interrupt Service Routine (ISR)

- A predefined OS routine
- Runs when an interrupt occurs
- Responsibilities:
  - determine which device interrupted
  - read data and/or error codes
  - mark I/O as complete
  - wake the blocked process
- Designed to be **short and fast**

---

## Key Benefit: Overlap of Computation and I/O

- CPU runs another process while I/O is in progress
- Disk and CPU work in parallel
- Leads to much better utilization of hardware resources

---

## Timeline Comparison

### Without Interrupts (Polling)
- CPU waits idly while device is busy
- No useful work is done during I/O

### With Interrupts
- CPU executes another process during I/O
- When I/O completes, interrupt resumes the original process

---

## Interrupts Are Not Always Better

Interrupts have **overhead**:
- context switching
- interrupt handling
- scheduler involvement

For **very fast devices**:
- polling may be cheaper than interrupt overhead

---

## Rule of Thumb (Exam-Important)

- **Fast device → polling may be better**
- **Slow device → interrupts are better**

---

## Hybrid (Two-Phase) Approach

Used when device speed is:
- unknown
- variable

Approach:
1. Poll briefly
2. If device not finished, switch to interrupts

Combines:
- low latency (polling)
- efficiency (interrupts)

---

## Interrupt Overload and Livelock

- Too many interrupts can overwhelm the OS
- OS spends all time handling interrupts
- User processes do not run → **livelock**

Common in:
- high-speed networks
- packet-heavy workloads

In such cases:
- polling may provide better control

---

## Interrupt Coalescing

Technique to reduce interrupt overhead:
- Device delays interrupt delivery
- Multiple events are grouped into one interrupt

Benefits:
- fewer interrupts
- lower CPU overhead

Trade-off:
- increased latency

---

## Common Exam Traps

- Interrupts are always better than polling ❌
- Polling is always inefficient ❌
- Interrupts allow overlap of CPU and I/O ✅
- Polling can be better for fast devices ✅
- Too many interrupts can cause livelock ✅

---

## One-Sentence Takeaway (Exam-Ready)

**Interrupts reduce CPU overhead by overlapping computation and I/O, but introduce handling costs and are most beneficial for slow devices rather than fast ones.**

---
---

## 36.5 More Efficient Data Movement With DMA — Explained Simply

### Big Picture (Why this matters)
When a process performs I/O (e.g., writing data to disk), **data must be moved between main memory and the device**.  
The key question is:

> **Who should move the data — the CPU or hardware?**

This section explains **why letting the CPU do it is inefficient** and **how DMA solves the problem**.

---

## Programmed I/O (PIO): The Problem

With **Programmed I/O (PIO)**:
- The **CPU copies data itself**
- One word (or byte) at a time
- From **memory → device**

### What happens with PIO
1. Process 1 runs on the CPU
2. It requests an I/O operation (e.g., disk write)
3. The OS starts the I/O
4. The **CPU repeatedly copies data**:
   - Read from memory
   - Write to device
5. Only after copying finishes does the device actually work

### Why this is bad
- The CPU is:
  - Busy doing **simple, repetitive work**
  - Unable to run other processes
- The CPU is **wasted on a task that hardware can do**

**Key intuition**  
> The CPU is too valuable to spend time copying data.

---

## What We Want Instead
We want:
- The CPU to **start the I/O**
- Then **go do useful work**
- While data movement happens **in parallel**

This leads to the core question:

> How can we move data **without occupying the CPU**?

---

## Direct Memory Access (DMA): The Solution

### What is DMA?
**Direct Memory Access (DMA)** is a hardware mechanism that:
- Transfers data **directly** between:
  - Main memory
  - An I/O device
- **Without CPU involvement for each word**

---

## How DMA Works (Step by Step)

### Step 1 — Setup (CPU involved briefly)
The OS programs the DMA controller with:
- Memory address of the data
- Size of the transfer
- Target device

This setup is **fast**.

---

### Step 2 — Transfer (CPU not involved)
- The **DMA controller copies data on its own**
- The CPU is **free**
- The OS schedules another process (e.g., Process 2)

✔ CPU and device operate **in parallel**

---

### Step 3 — Completion (Interrupt)
- When the DMA transfer finishes:
  - The DMA controller raises an **interrupt**
- The OS is notified that the I/O is complete

---

## Why DMA Is Better Than PIO

| Aspect | PIO | DMA |
|-----|----|----|
| Data movement | CPU copies data | Hardware copies data |
| CPU usage | Blocked | Free |
| Parallelism | None | CPU and I/O overlap |
| Performance | Poor | Much better |

**Key intuition**  
> DMA turns I/O into a background task.

---

## Timeline Intuition (Exam-Relevant)

### PIO Timeline
- CPU runs Process 1
- CPU copies data (blocked)
- Disk works
- CPU resumes

### DMA Timeline
- CPU runs Process 1
- DMA copies data
- CPU runs Process 2
- Disk works
- Interrupt occurs
- CPU resumes Process 1

✔ **Overlap of computation and I/O**

---

## One-Sentence Takeaway (Exam-Ready)

> **Direct Memory Access (DMA) improves I/O efficiency by allowing hardware to transfer data directly between memory and devices, freeing the CPU to execute other processes while the transfer occurs.**

---
---

## 36.6 Methods of Device Interaction — Explained Simply (Exam-Focused)

### The core problem
When a program wants to read from or write to a device (disk, keyboard, network card), **how does the operating system actually communicate with the hardware?**

The OS must send:
- **commands** (what to do)
- **data** (what to read/write)
- and receive **status** (busy, done, error)

Over time, **two main methods** for device communication evolved.

---

## Method 1: Explicit I/O Instructions (Port-Mapped I/O)

### Idea
Devices are **not part of normal memory**.  
Instead, the CPU provides **special instructions** dedicated to I/O.

### How it works
- The CPU executes special I/O instructions (e.g., `in`, `out` on x86)
- Each device is identified by a **port number**
- Data is transferred directly to/from device registers

### Privilege rule (very important)
- These instructions are **privileged**
- Only the OS (kernel mode) can execute them
- User programs are **not allowed**

### Why this restriction exists
If user programs could directly access devices:
- They could overwrite disks
- Spy on keyboard input
- Take control of the system

Thus, the OS acts as the **only trusted intermediary**.

### Mental model
- Memory = normal rooms
- Devices = locked control room
- OS = the only one with the key

---

## Method 2: Memory-Mapped I/O (MMIO)

### Idea
Devices are made to **look like memory**.

Their registers are assigned addresses inside the normal memory address space.

### How it works
- Device registers are mapped to specific memory addresses
- The OS uses **normal load/store instructions**
- Hardware detects that the address belongs to a device
- The access is routed to the device instead of RAM

### From the CPU’s perspective
- Accessing memory
- Accessing a device  

➡️ Looks exactly the same

### Still protected
- Only the OS can access device memory addresses
- User programs are prevented by hardware protection

---

## Why memory-mapped I/O is popular

- No special CPU instructions needed
- Simpler CPU and compiler design
- Works naturally with modern architectures

That’s why many modern systems (ARM, RISC-V, GPUs) prefer MMIO.

---

## Which approach is better?

There is **no universally better choice**.

| Approach | Key characteristic |
|--------|-------------------|
| Explicit I/O | Uses special instructions and ports |
| Memory-mapped I/O | Uses normal memory instructions |

Both approaches are still widely used today, sometimes **in the same system**.

---

## How this fits into the OS design

- Devices expose **registers** (status, command, data)
- The OS (via drivers) reads/writes these registers
- Interrupts and DMA are used to improve efficiency
- Applications never access devices directly

---

## One-sentence takeaway (exam-ready)

> The operating system communicates with hardware devices either through special privileged I/O instructions that access device ports, or by mapping device registers into the memory address space and accessing them with normal load/store instructions, with access restricted to kernel mode for protection.

---
---

## 36.7 Fitting Into the OS: The Device Driver

### The core problem
- Hardware devices have **very different interfaces**:
  - different registers
  - different commands
  - different protocols (SCSI, SATA, USB, etc.)
- The **operating system wants to remain generic**:
  - file systems should not depend on disk type
  - applications should not depend on device details

**Goal:** keep most of the OS **device-neutral**.

---

## The solution: abstraction via device drivers

- The OS uses **abstraction** to isolate device-specific behavior.
- A **device driver** is a piece of OS code that:
  - understands a **specific device**
  - hides hardware details from the rest of the OS
  - translates **generic OS requests** into **device-specific commands**

All device-specific logic is **encapsulated inside the driver**.

---

## Layered structure (Linux-style example)

From top to bottom:

### 1. Application layer
- Uses standard system calls:
  - `open`, `read`, `write`, `close`
- Completely **unaware of device type**

### 2. File system
- Works with files and blocks
- Issues **generic block read/write requests**
- Does not know whether storage is SATA, USB, or SCSI

### 3. Generic block layer
- Provides a **uniform block interface**
- Routes requests to the correct device driver

### 4. Device driver
- Knows **exact hardware details**
- Issues real commands to the device
- Handles:
  - interrupts
  - DMA
  - errors
  - device-specific protocols

Only this layer interacts directly with hardware.

---

## Why this design is important

### Advantages
- **Device neutrality**
  - same file system works on many devices
- **Extensibility**
  - new hardware → add a new driver
- **Cleaner OS design**
  - core subsystems stay simple and generic

---

## Downsides of abstraction (exam-relevant)

### 1. Loss of device-specific features
- Some devices (e.g., SCSI) support rich error reporting
- Generic interfaces hide these details
- Higher layers only see generic I/O errors

### 2. Large and fragile codebase
- Device drivers form a **huge portion of kernel code**
  - often **over 70%**
- Drivers are frequently written by third parties
- Common source of:
  - kernel bugs
  - system crashes

---

## Key exam traps to avoid
- ❌ Applications communicate directly with devices  
- ❌ File systems issue hardware commands  
- ❌ All OS code must understand device specifics  

Correct view:
- **Only device drivers know hardware details**
- All other layers use **generic interfaces**

---

## One-sentence takeaway (exam-ready)

> Device drivers provide an abstraction layer that encapsulates all device-specific behavior, allowing the rest of the operating system to remain device-neutral while interacting with hardware through uniform, generic interfaces.


---
---

## 36.8 Case Study: A Simple IDE Disk Driver

### Purpose of this section
- Demonstrates how **abstract I/O concepts** (registers, polling, interrupts, drivers) are used in a **real device driver**
- Focus is on **control flow and responsibilities**, not on memorizing code or register addresses

---

## What an IDE disk looks like to the OS

An IDE disk exposes a **register-based interface**.  
The OS controls the disk **only by reading and writing registers**.

### Logical groups of registers
1. **Control registers**
   - Enable/disable interrupts
   - Reset the device

2. **Command block registers**
   - Specify the operation:
     - sector count
     - logical block address (LBA)
     - drive number (master/slave)
     - read or write command

3. **Status register**
   - Indicates current device state:
     - busy
     - ready
     - request for data
     - error

4. **Error register**
   - Contains details when an error occurs
   - Only meaningful if the status register reports an error

---

## High-level IDE disk protocol (exam-relevant)

### Step 1: Wait for the disk to be ready
- The OS checks the **status register**
- Proceeds only when:
  - the disk is not busy
  - the disk is ready

---

### Step 2: Configure the request
- The OS writes parameters into command registers:
  - number of sectors
  - starting block (LBA)
  - target drive

---

### Step 3: Start the I/O operation
- The OS issues a **READ** or **WRITE** command
- The disk begins executing the request

---

### Step 4: Transfer data
- **Write**:
  - OS sends data to the disk
- **Read**:
  - disk provides data when ready
- Data transfer may be:
  - programmed I/O (PIO), or
  - handled by DMA

---

### Step 5: Handle interrupts
- The disk raises an **interrupt** when:
  - a sector is transferred, or
  - the entire request completes
- The interrupt handler:
  - completes the data transfer (if needed)
  - wakes the sleeping process
  - starts the next queued request

---

### Step 6: Error handling
- After each operation:
  - the OS checks the status register
- If an error is reported:
  - the OS reads the error register
  - translates the hardware error into an OS-level error

---

## Driver responsibilities (conceptual)

An IDE disk driver typically:
1. **Queues I/O requests**
2. **Starts requests** by programming device registers
3. **Puts processes to sleep** while I/O is in progress
4. **Handles interrupts**
   - finishes requests
   - wakes processes
   - launches the next request

Only one request is active at a time; others wait in a queue.

---

## What is not required to memorize
- Register addresses
- Bit-level meanings
- Driver source code

These details are illustrative, not exam material.

---

## What must be understood for exams
- Devices are controlled via **registers**
- Drivers implement **device-specific protocols**
- I/O is **asynchronous**
- Processes **block during I/O**
- Interrupts signal completion
- Drivers serialize access to hardware

---

## One-sentence takeaway (exam-ready)

> A disk device driver controls hardware by following a strict register-based protocol, queues and serializes I/O requests, blocks processes during disk operations, and uses interrupts to complete requests and wake waiting processes.

---
---
# Chapter 36 — I/O Devices  
## Full Conceptual Summary (Exam-Focused)

---

## What this chapter is really about

This chapter answers one fundamental OS question:

> **How does the operating system interact efficiently, safely, and generically with hardware devices?**

Devices are **slow**, **diverse**, and **hardware-specific**.  
The CPU and OS must be able to:
- control them  
- move data to/from them  
- avoid wasting CPU time  
- hide device details from most of the OS  

This chapter explains how that is achieved.

---

## 1. System Architecture and I/O Buses

A typical system is **hierarchical**:

- **CPU ↔ Main memory** via a fast **memory bus**
- **High-performance devices** (e.g., graphics) via a **general I/O bus** (e.g., PCI)
- **Slow devices** (disks, USB, etc.) via **peripheral buses**

### Why this hierarchy exists
- Faster buses are **expensive** and **short**
- Slower buses allow **many devices**
- Low-performance devices are placed **farther from the CPU**

### Key exam idea
**Not all devices are equal** → system architecture reflects performance and cost trade-offs.

---

## 2. The Canonical Device Model

From the OS point of view, every device looks roughly the same:

- A **hardware interface** (registers)
- An **internal implementation** (hidden from the OS)

### Typical device registers
- **Status registers** (busy, ready, error)
- **Command registers** (what to do)
- **Data registers** (send/receive data)

The OS **never touches device internals** — it only reads and writes registers.

### Key exam idea
**Devices are controlled entirely via registers.**

---

## 3. The Canonical I/O Protocol (Polling & PIO)

The simplest interaction protocol:

1. Poll the status register until the device is ready  
2. Write data to the device  
3. Issue a command  
4. Poll again until the operation completes  

This uses **Programmed I/O (PIO)**:
- CPU moves data itself
- Simple to implement
- **Very inefficient**

### Why PIO is bad
- CPU wastes time waiting
- CPU wastes time copying data

### Key exam idea
**PIO is simple but wastes CPU time.**

---

## 4. Interrupts: Reducing CPU Overhead

To avoid busy waiting, devices can use **interrupts**:

- OS issues I/O request
- Calling process **blocks**
- CPU runs another process
- Device finishes and raises an **interrupt**
- OS interrupt handler completes the request
- Waiting process is woken up

This enables **overlap of computation and I/O**.

### Important caveats
- Interrupts are **not always better**
- For very fast devices, interrupt overhead may dominate
- Polling or hybrid schemes can be better

### Key exam idea
**Interrupts trade polling overhead for context-switch overhead.**

---

## 5. DMA: Efficient Data Movement

PIO still has a major flaw:
- CPU copies data **word-by-word**

### Direct Memory Access (DMA)
- OS programs a **DMA controller**
- DMA transfers data directly between memory and device
- CPU continues running other work
- DMA raises an interrupt when done

DMA is essential for **large data transfers** (e.g., disk I/O).

### Key exam idea
**DMA offloads data movement from the CPU.**

---

## 6. How the OS Communicates with Devices

Two main methods:

### 1. Explicit I/O instructions (Port-mapped I/O)
- Special CPU instructions (`in`, `out`)
- Devices accessed via ports
- Privileged (kernel-only)

### 2. Memory-mapped I/O
- Device registers mapped into memory space
- Accessed using normal loads/stores
- Hardware redirects accesses to devices

Both approaches are widely used today.

### Key exam idea
**Device access is always restricted to kernel mode.**

---

## 7. Device Drivers: Making the OS Device-Neutral

Devices differ wildly, but the OS wants to stay generic.

### Solution: Device drivers

A device driver:
- Encapsulates device-specific details
- Implements device protocols
- Exposes a **generic interface** to the rest of the OS

### Layered design (Linux example)
- Application  
- File system  
- Generic block layer  
- Device driver  
- Hardware  

### Pros
- Portability
- Modularity
- Extensibility

### Cons
- Loss of device-specific features
- Huge amount of kernel code
- Drivers are a major source of OS bugs

### Key exam idea
**Only drivers know hardware details.**

---

## 8. Case Study: IDE Disk Driver

The IDE example illustrates:
- Strict hardware protocols
- Request queuing
- Process sleeping during I/O
- Interrupt-driven completion

You are **not expected** to memorize registers or code.

### What matters conceptually
1. Wait for device readiness  
2. Program registers  
3. Issue command  
4. Block process  
5. Handle interrupt  
6. Wake process and start next request  

### Key exam idea
**Disk I/O is serialized and interrupt-driven.**

---

## What exam questions usually test from this chapter

They focus on:
- Polling vs interrupts
- PIO vs DMA
- Why DMA is needed
- Why interrupts are not always best
- Memory-mapped vs port-mapped I/O
- Role of device drivers
- Why drivers are isolated
- How I/O overlaps with CPU execution

They do **not** test:
- Register addresses
- Device-specific bit fields
- Driver source code

---

## One-paragraph exam-ready summary

Operating systems interact with hardware devices through register-based interfaces using polling, interrupts, and DMA to balance simplicity and efficiency. While programmed I/O is simple, it wastes CPU time; interrupts allow overlap of computation and I/O, and DMA further improves performance by offloading data movement from the CPU. Device drivers encapsulate hardware-specific details behind generic interfaces, allowing the rest of the OS to remain device-neutral while safely and efficiently managing diverse I/O hardware.

---
---

# Chapter 37 — Hard Disk Drives

## Introduction

Hard disk drives (HDDs) are one of the most important I/O devices in traditional operating systems because they provide **persistent storage**. Unlike main memory (RAM), disk data **survives power loss**, which makes disks the foundation for:

- File systems  
- Databases  
- Virtual memory (swap space)  
- Long-term data storage  

Much of operating system design—especially **file system layout, buffering, and scheduling**—is based on the **performance characteristics of disks**.  
Therefore, before building higher-level storage software, the OS must understand **how disks store data and how data is accessed**.

### The Crux

> **How do hard disks store and access data, and how can the OS use this knowledge to improve performance?**

---

## 37.1 — The Interface

### Disk as an Array of Blocks

From the operating system’s perspective, a hard disk presents a **simple abstraction**:

- The disk is divided into **fixed-size blocks** (sectors)
- Traditionally, each block is **512 bytes**
- Blocks are numbered from **0 to N−1**

The disk can be viewed as: 
Disk = array of blocks
Address space = block numbers (0 … N−1) 

The OS interacts with the disk using only:
- `read(block_number)`
- `write(block_number)`

All mechanical details are hidden behind this interface.

---

### Multi-Block I/O

Although the interface allows single-block access:

- Operating systems usually perform **multi-block I/O**
- Typical sizes are **4 KB or more**
- This reduces overhead and improves performance

However, this introduces an important limitation.

---

### Atomicity Guarantee

Hard disks provide **only one atomicity guarantee**:

> **A single 512-byte block write is atomic**

This means:
- The entire block is written, or
- The write does not happen at all

For **multi-block writes**:
- A crash or power loss may leave the disk **partially updated**
- Some blocks written, others not

This problem is called a **torn write**.

This is why file systems rely on:
- Journaling
- Logging
- Copy-on-write

---

### The “Unwritten Contract” of Disk Performance

Although not stated in the interface, OS designers rely on these disk properties:

- Accessing **nearby blocks is faster** than far-apart blocks
- **Sequential access** is much faster than random access

Reasons:
- Disk heads must move (seek time)
- Platters must rotate (rotation latency)

Examples:
- Reading blocks `100, 101, 102` → fast  
- Reading `100, 50000, 12` → slow  

---

### Why the OS Cares

Because of these properties, the OS tries to:

- Place related data **close together on disk**
- Perform **sequential reads and writes**
- Schedule disk requests to **minimize head movement**

These ideas directly motivate:
- Disk scheduling algorithms
- File layout strategies
- Read-ahead and buffering policies

---

## Exam-Ready Takeaways

- Disks expose a **block-based interface**
- Blocks are addressed from **0 to N−1**
- **Only single-block writes are atomic**
- Multi-block writes can cause **torn writes**
- **Sequential access is much faster than random access**
- Disk performance is dominated by **mechanical delays**
- OS design must respect disk behavior for performance

---
---

## 37.2 — Basic Geometry

This section explains the **physical structure of a modern hard disk** and the mechanical components that dominate disk performance.

---

### Platters and Surfaces

- A **platter** is a circular, rigid disk on which data is stored
- Data is stored **magnetically**, allowing persistence even when power is off
- A disk drive may contain **one or more platters**
- Each platter has **two sides**, called **surfaces**
- Each surface can store data independently

**Materials**
- Platters are typically made of hard material (e.g., aluminum)
- Coated with a **thin magnetic layer** to store bits

---

### Spindle and Rotation

- All platters are mounted on a central **spindle**
- The spindle is connected to a motor that spins platters at a **constant speed**
- Rotation speed is measured in **RPM (rotations per minute)**

**Typical values**
- Common speeds: **7,200 RPM – 15,000 RPM**
- Example:
  - 10,000 RPM  
  - One rotation ≈ **6 ms**

> The OS often reasons in terms of **rotation time**, not RPM.

---

### Tracks

- Data on each surface is organized into **concentric circles**
- Each concentric circle is called a **track**
- A single surface contains **thousands of tracks**
- Tracks are packed extremely tightly:
  - Hundreds of tracks can fit in the width of a human hair

---

### Disk Head

- To read or write data, the disk uses a **disk head**
- There is **one head per surface**
- The disk head:
  - Reads magnetic patterns (read)
  - Changes magnetic patterns (write)

---

### Disk Arm

- Disk heads are attached to a **disk arm**
- The disk arm moves **radially across the surface**
- Movement positions the head over the **desired track**

This movement is mechanical and slow compared to CPU and memory access.

---

### Key Mechanical Movements

Accessing data may require:
1. **Seeking** — moving the disk arm to the correct track
2. **Rotation** — waiting for the desired sector to rotate under the head

These mechanical delays dominate disk access time.

---

### Exam-Ready Takeaways

- Data is stored on **platters**, each with two **surfaces**
- Platters spin at a **fixed RPM**
- One rotation takes a few milliseconds
- Data is organized into **tracks** (concentric circles)
- Each surface has one **disk head**
- Disk heads move using a **disk arm**
- Disk performance is limited by **mechanical motion**, not computation


---
---

## 37.3 — A Simple Disk Drive

This section builds intuition for **disk I/O performance** by starting with a very simple disk model and gradually adding realistic components. The goal is to understand **where disk latency comes from**, which is a frequent exam topic.

---

### Single-Track Disk Model

Assume a highly simplified disk:

- One platter
- One track
- 12 sectors per track
- Each sector = 512 bytes
- Sectors numbered 0–11
- One disk head fixed on the track

The platter rotates at a constant speed.

If the OS requests a sector:
- No head movement is needed
- The disk must **wait for the sector to rotate under the head**

---

### Rotational Delay

**Rotational delay** is the time spent waiting for the desired sector to arrive under the disk head.

- Let `R` = time for one full rotation
- Average rotational delay ≈ `R / 2`
- Worst-case delay ≈ `R`

Example:
- If the disk rotates at 10,000 RPM  
- One rotation ≈ 6 ms  
- Average rotational delay ≈ 3 ms

Even when the head is on the correct track, rotation alone causes significant delay.

---

### Multiple Tracks: Seek Time

Real disks have **millions of tracks**, not just one.

If the requested sector is on a different track:
- The disk arm must move the head → **seek**

**Seek time** is one of the most expensive disk operations.

Seek consists of:
1. Acceleration
2. Constant-speed movement
3. Deceleration
4. Settling (precise alignment)

Settling time alone can be **0.5–2 ms**.

---

### Complete Disk I/O Timeline

A disk access generally involves three phases:

1. **Seek time** — move head to correct track
2. **Rotational delay** — wait for sector to rotate under head
3. **Transfer time** — read or write the data

This ordering is fundamental for disk performance analysis.

---

### Track Skew

When reading sequential blocks across tracks:
- The head must move to the next track
- The platter continues rotating during this movement

Without adjustment:
- The next sector may already have passed
- The disk would wait almost a full rotation

**Track skew** solves this:
- Sectors on adjacent tracks are intentionally offset
- Allows sequential reads to continue efficiently across tracks

Track skew exists purely to improve sequential performance.

---

### Multi-Zone Disks

Outer tracks:
- Have larger circumference
- Can hold more sectors than inner tracks

Modern disks use **zones**:
- Each zone has a fixed number of sectors per track
- Outer zones contain more data per rotation

Implication:
- Sequential reads on outer tracks are faster
- File systems often place frequently accessed data there

---

### Disk Cache (Track Buffer)

Disks include a small internal cache (e.g., 8–16 MB):

- When a sector is read, the disk may read the entire track
- Subsequent accesses to nearby sectors are served from cache

This significantly improves:
- Sequential access
- Repeated access to the same track

---

### Write Policies

Disks may acknowledge writes in two ways:

**Write-through**
- Acknowledge after data is written to disk
- Slower but safer

**Write-back**
- Acknowledge once data is in disk cache
- Faster but dangerous if power fails

File systems must handle this carefully (e.g., journaling).

---

### Dimensional Analysis (Exam Technique)

Dimensional analysis is used to compute disk timing:

- RPM → milliseconds per rotation
- MB/s → milliseconds per block

Examples:
- 10,000 RPM → 6 ms per rotation
- 100 MB/s transfer rate → ~5 ms to transfer 512 KB

These calculations appear frequently in exams.

---

### One-Sentence Takeaway (Exam-Ready)

**Disk I/O time is dominated by mechanical delays: seek first, then rotation, then data transfer, with optimizations like skewing, caching, and zoning used to reduce these costs.**

<img width="408" height="204" alt="Снимок экрана 2026-01-25 в 17 41 31" src="https://github.com/user-attachments/assets/380d91b3-fe36-4c70-83b2-b7388bbf4d0d" />

---
---

## 37.4 I/O Time: Doing the Math

To understand **hard disk performance**, we model the total time needed to service a single disk I/O request. Disk I/O is slow not because of data transfer, but because of **mechanical delays**.

---

### Total Disk I/O Time

A disk request consists of **three main components**:

\[
T_{I/O} = T_{seek} + T_{rotation} + T_{transfer}
\]

Where:

- **\(T_{seek}\)** – time to move the disk head to the correct track  
- **\(T_{rotation}\)** – time waiting for the desired sector to rotate under the head  
- **\(T_{transfer}\)** – time to actually read/write the data  

---

### 1. Seek Time (\(T_{seek}\))

- Mechanical movement of the disk arm
- Typically the **largest contributor** to I/O time for random workloads
- Manufacturers usually report **average seek time**
- A full seek (outermost → innermost track) can take **2–3× longer** than average

---

### 2. Rotational Delay (\(T_{rotation}\))

- Disk spins at a fixed speed (RPM)
- Average rotational delay = **½ rotation**

Example:
- 15,000 RPM  
  - 250 rotations/sec  
  - 1 rotation = 4 ms  
  - **Average rotational delay ≈ 2 ms**

---

### 3. Transfer Time (\(T_{transfer}\))

- Time to move data once the head is positioned
- Computed as:

\[
T_{transfer} = \frac{\text{Data size}}{\text{Transfer rate}}
\]

- Usually **very small** compared to seek and rotation (microseconds vs milliseconds)

---

### From Time to Throughput

Often we compare disks using **I/O rate** instead of time:

\[
R_{I/O} = \frac{\text{Size}_{transfer}}{T_{I/O}}
\]

This gives throughput in **MB/s**.

---

### Random vs Sequential Workloads

#### Random I/O
- Each request incurs:
  - Seek
  - Rotation
  - Small transfer
- Very slow overall

Example (4 KB random reads):
- High-performance disk: ~0.66 MB/s
- Capacity disk: ~0.31 MB/s

#### Sequential I/O
- One seek + one rotation
- Large continuous transfer
- Near peak disk bandwidth

Example:
- Sequential reads: **100+ MB/s**

---

### Key Observations

- **Random I/O is 200–300× slower than sequential I/O**
- Transfer time is negligible for small random requests
- Seek and rotation dominate performance
- High-performance disks matter most for random workloads

---

### Design Rule (Exam-Ready)

> **Always try to access disks sequentially; random I/O is extremely expensive.**

---

### Tip: Use Disks Sequentially

- Read/write data in **large contiguous chunks**
- Avoid small random I/Os
- Filesystems and databases are designed to enforce sequential access patterns

---

### Summary

- Disk I/O time = seek + rotation + transfer
- Seek + rotation dominate random I/O
- Sequential access maximizes performance
- Disk scheduling and layout exist mainly to **reduce seek and rotation costs**

---
---
## 37.5 Disk Scheduling

Because disk I/O is extremely expensive compared to CPU operations, the operating system plays an important role in deciding the **order** in which disk I/O requests are serviced. Given a set of pending disk requests, the **disk scheduler** examines them and decides which one should be issued next.

Unlike CPU job scheduling, disk scheduling has an advantage: the OS can often **estimate how long a disk request will take**, because it can approximate the **seek time** and **rotational delay**. Using this information, the disk scheduler tries to minimize overall service time and thus behaves similarly to the **Shortest Job First (SJF)** principle.

---

### Shortest Seek Time First (SSTF)

One early disk scheduling algorithm is **Shortest Seek Time First (SSTF)** (also called shortest-seek-first).

**Idea:**  
Service the disk request whose track is **closest to the current head position**, minimizing seek distance.

**Example:**  
If the head is currently on the inner track and there are requests for:
- sector 21 (middle track)
- sector 2 (outer track)

SSTF will:
1. Service sector 21 first (shorter seek)
2. Then service sector 2

This works well in simple cases and reduces seek time.

---

### Problems with SSTF

SSTF is **not ideal** for two main reasons:

#### 1. Lack of disk geometry knowledge
The OS does not actually see tracks and cylinders; it sees a **linear array of blocks**.  
As a result, SSTF is usually approximated as **Nearest-Block-First (NBF)**, where the closest block number is chosen instead.

#### 2. Starvation
SSTF can cause **starvation**.

If there is a steady stream of requests near the current head position, requests farther away may **never be serviced**. This makes SSTF unfair in practice.

---

### Elevator Scheduling (SCAN)

To fix starvation, **SCAN** was introduced.

**Idea:**  
Move the disk head back and forth across the disk, servicing requests in order as it moves — just like an **elevator**.

- A single pass in one direction is called a **sweep**
- Requests that arrive after the head has passed their track must wait for the **next sweep**

This guarantees that all requests will eventually be serviced.

---

### Variants of SCAN

#### F-SCAN
- Freezes the queue at the start of a sweep
- New requests are placed into a separate queue
- Prevents starvation of far-away requests

#### C-SCAN (Circular SCAN)
- Head moves in **one direction only**
- When it reaches the end, it jumps back to the beginning
- More uniform wait times than SCAN

SCAN and its variants are often called **elevator algorithms** because of their movement pattern.

---

### Limitations of SCAN and SSTF

Although SCAN prevents starvation, it still does **not fully implement SJF**:

- It **ignores rotational delay**
- Two requests on the same track may still have very different service times depending on rotation

This leads to a deeper question.

---

### Shortest Positioning Time First (SPTF)

**SPTF (Shortest Positioning Time First)** attempts to choose the request with the **minimum total positioning time**, considering:

- Seek time
- Rotational delay

**Key insight:**  
Which request is “best” depends on the **relative cost of seek vs rotation**.

- If seek dominates → SSTF is good enough
- If rotation dominates → a farther seek might still be faster overall

However, implementing SPTF is **very difficult at the OS level**, because:
- The OS does not know exact head position
- Rotation phase is hidden inside the drive

As a result, **SPTF is usually implemented inside the disk controller**, not the OS.

---

### Disk Scheduling in Modern Systems

Modern disks:
- Can queue multiple outstanding requests
- Have **internal schedulers** that know exact geometry and head position

Typical OS behavior:
1. OS selects a batch of requests (e.g., 16)
2. Sends them to the disk
3. Disk reorders them internally using an SPTF-like algorithm

---

### I/O Merging

Another important disk scheduling optimization is **I/O merging**.

**Idea:**  
If there are multiple adjacent block requests (e.g., block 33 and 34), merge them into a **single larger request**.

**Benefits:**
- Fewer disk commands
- Less overhead
- Better sequential access performance

This optimization is especially important at the OS level.

---

### Work-Conserving vs Non-Work-Conserving Scheduling

- **Work-conserving:**  
  If any request exists, immediately issue it to the disk.

- **Non-work-conserving:**  
  Wait briefly before issuing a request, allowing potentially better requests to arrive.

Research shows that **waiting a little** can sometimes **improve overall efficiency**, but deciding how long to wait is tricky and system-dependent.

---

### One-Sentence Takeaway (Exam-Ready)

Disk scheduling exists because disk I/O is expensive; simple algorithms like SSTF minimize seek but cause starvation, elevator algorithms prevent starvation, and modern disks combine OS-level batching with internal scheduling that accounts for both seek and rotation.

---
---




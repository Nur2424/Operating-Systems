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




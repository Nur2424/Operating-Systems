# Operating Systems — Exam-Oriented Notes

This repository contains **concise, exam-focused notes** for an Operating Systems course.  
The content is distilled from textbook chapters and **validated against multiple past exam papers** to ensure relevance and efficiency.

The goal of this repository is simple:

> **Use these notes instead of the textbook when revising for the exam.**

---
Each file groups **multiple textbook chapters** that are conceptually and exam-wise related.

---

## 📘 Chapter Coverage by File

### 📄 `01-os-basics.md`
**Foundational OS concepts**
- General operating system overview
- User mode vs kernel mode
- System calls
- OS as an abstraction layer

(Used as conceptual grounding for the rest of the course)

---

### 📄 `02-processes-and-threads.md`
**Covers textbook chapters: 4, 5, 26, 27**

Topics include:
- Process abstraction
- Process states and transitions
- Process creation (`fork`)
- Threads vs processes
- Thread address-space sharing
- Thread mapping models
  - Many-to-one
  - One-to-one
  - Many-to-many
- Context switching basics

**Exam focus**:
- State transitions
- What is shared vs private
- Thread models MCQs

---

### 📄 `03-cpu-scheduling.md`
**Covers textbook chapters: 7, 8, 9, 10**

Topics include:
- CPU scheduling goals
- Preemptive vs non-preemptive scheduling
- Scheduling algorithms:
  - FCFS
  - SJF / SRTF
  - Round Robin
- Time slice (quantum)
- Waiting time
- Turnaround time
- Completion time
- CPU-bound vs I/O-bound processes

**Exam focus**:
- Scheduling tables
- Average waiting time calculations
- Scheduler takeover conditions

---

### 📄 `04-concurrency-and-deadlocks.md`
**Covers textbook chapters: 28, 30, 31, 32**

Topics include:
- Locks and critical sections
- Hardware primitives:
  - Test-and-set
  - Compare-and-swap
  - Load-linked / Store-conditional
- Spin locks vs blocking locks
- Condition variables:
  - `wait()` / `signal()`
  - Mesa semantics
  - Why `while` is mandatory
- Semaphores:
  - Binary semaphores (locks)
  - Semaphores as condition variables
- Classical problems:
  - Producer / Consumer
  - Reader / Writer
  - Dining Philosophers
- Common concurrency bugs:
  - Atomicity violations
  - Order violations
- Deadlocks:
  - Definition
  - Resource Allocation Graphs
  - Four necessary conditions
  - Prevention, avoidance, detection, recovery

**Exam focus**:
- Deadlock conditions
- Bug classification
- Correct synchronization patterns
- Tricky semaphore questions

---

### 📄 `05-memory-management.md`
**Covers textbook chapters: 13, 15, 18, 21, 22**

Topics include:
- Address space abstraction
- Code, stack, heap
- Function calls and stack frames
- Virtual vs physical addresses
- Address translation
- Hardware-based relocation
- Paging fundamentals
- Swap space
- Page faults
- Page replacement intuition
- Virtual memory goals:
  - Transparency
  - Efficiency
  - Protection
  - Isolation

**Exam focus**:
- Address binding
- Page table size
- Page fault handling
- Swap vs RAM
- Virtual memory MCQs

---

### 📄 `06-io-and-storage.md`
**Covers textbook chapters: 36, 37**

Topics include:
- I/O devices overview
- Interrupts vs polling
- DMA basics
- Disk structure
- Disk access times:
  - Seek time
  - Rotational delay
  - Transfer time
- Disk scheduling algorithms:
  - FCFS
  - SSTF
  - SCAN / C-SCAN

**Exam focus**:
- Disk scheduling calculations
- Seek distance questions
- I/O timing formulas

---

## 🎯 How These Notes Were Written

- Built incrementally while studying
- Refined using **past exam questions**
- Focused on:
  - What is actually tested
  - Conceptual understanding
  - Avoiding common mistakes
- Minimal code, maximum explanation

---

## ✅ How to Use This Repo

**During study**
- Read textbook once (if needed)
- Use these notes to consolidate

**Before the exam**
1. Read files `02` → `06`
2. Finish with `07-exam-cheatsheet.md`
3. Practice past exams

If you understand everything here, **you are ready**.

---

## 🧠 Final Note

These notes are intentionally:
- Clear
- Compact
- Exam-driven

They are not meant to replace understanding —  
they are meant to **lock it in**.

Good luck. 💪


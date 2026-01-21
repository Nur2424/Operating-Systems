	•	Ch 28 — Locks
	•	Ch 30 — Condition variables (conceptual)
	•	Ch 31 — Semaphores
	•	Ch 32 — Concurrency problems

Include:

	•	Critical section
	•	Race condition
	•	Mutex vs semaphore
	•	Deadlock:
	•	4 conditions
	•	prevention vs avoidance vs detection

---
---

# Chapter 28 — Locks

## 28.1 Locks: The Basic Idea

### The Problem: Atomicity in Concurrent Programs

In concurrent programming, we often want a sequence of instructions to execute **atomically**.  
However, even a simple statement such as: balance = balance + 1; 
is **not atomic**. At the machine level, it expands into multiple steps:
- load `balance` from memory
- add `1`
- store the result back to memory

If multiple threads execute these steps concurrently, their operations can **interleave**, leading to incorrect results (race conditions).

This problem occurs:
- on a single CPU (due to interrupts and context switches)
- on multiple CPUs (due to true parallel execution)

---

### The Solution: Locks

A **lock** is a synchronization primitive that enforces **mutual exclusion**.

- At most **one thread** can hold a lock at any time
- Only the thread holding the lock may enter the **critical section**
- Other threads attempting to acquire the lock must wait

Typical usage pattern: 

	lock(&mutex);
	balance = balance + 1;
	unlock(&mutex);

This guarantees that the critical section executes **as if it were a single atomic instruction**.

---

### Lock States

A lock is a shared variable with two conceptual states:

- **Free (unlocked)**  
  No thread holds the lock

- **Held (locked)**  
  Exactly one thread holds the lock (the *owner*)

Behavior:
- If a thread calls `lock()` and the lock is free → it acquires the lock
- If the lock is already held → the thread blocks or spins (implementation-dependent)
- When the owner calls `unlock()`:
  - the lock becomes free
  - one waiting thread may acquire it

---

### Ownership

- The thread that successfully acquires the lock is called the **owner**
- Only the owner is allowed to call `unlock()`
- Ownership ensures that no two threads are inside the critical section simultaneously

---

### Why Locks Matter

Locks give programmers limited but crucial control over concurrency:

- Threads are still scheduled by the OS
- Locks restrict *where* concurrency is allowed
- Critical sections become protected regions of code

Without locks:
- race conditions
- corrupted shared state
- nondeterministic behavior

With locks:
- correctness is enforced
- at the cost of reduced parallelism and potential performance overhead

---

### Key Takeaways

- Locks are variables used to protect critical sections
- They ensure **mutual exclusion**
- They make non-atomic instruction sequences behave atomically
- They transform uncontrolled concurrency into structured, safe execution

---
---

## 28.2 Pthread Locks

### What is a pthread mutex?
- A **pthread mutex** is the POSIX thread library’s implementation of a lock.
- It provides **mutual exclusion** between threads.
- If one thread is inside a critical section protected by a mutex, no other thread can enter it until the mutex is released.

---

### Lock and Unlock Semantics
- **Lock**
  - If the mutex is free → the calling thread acquires it and enters the critical section.
  - If the mutex is already held → the calling thread **blocks (sleeps)** until the mutex becomes available.
- **Unlock**
  - Releases the mutex.
  - If other threads are waiting, one of them will eventually acquire the mutex.

Key idea: **pthread mutexes block instead of spinning**.

---

### Ownership
- A mutex is **owned by the thread** that successfully locks it.
- Only the owner thread is allowed to unlock it.
- At any time:
  - Mutex is either **free**
  - Or **held by exactly one thread**

---

### Why pthread mutexes are important
- Prevent **race conditions** on shared data.
- Ensure **atomic execution of critical sections**.
- Are the standard and safe synchronization primitive for threads in UNIX-like systems.

---

### Coarse-Grained vs Fine-Grained Locking
- **Coarse-grained locking**
  - One mutex protects a large section of code or many data structures.
  - Easier to implement.
  - Reduces parallelism.
- **Fine-grained locking**
  - Different mutexes protect different data structures or variables.
  - Allows higher concurrency.
  - More complex and error-prone.

Exam insight:
- Fine-grained locking improves performance by allowing multiple threads to execute different critical sections simultaneously.

---

### What pthread mutexes do NOT guarantee
- No guarantee of **fairness** (a waiting thread may starve).
- Do not prevent **deadlocks** automatically.
- Do not disable interrupts.
- Do not control thread scheduling order.

---

### Exam Summary
- A pthread mutex is a **blocking synchronization primitive** that enforces mutual exclusion.
- Only one thread can hold the mutex and execute the protected critical section at a time.
- Mutexes trade simplicity and correctness for potential contention and reduced parallelism.

---
--- 

## 28.3 Building a Lock

### Motivation
Up to this point, locks have been used as a **programmer abstraction** (e.g., `lock()` / `unlock()`), without considering how they are implemented internally.  
This section shifts focus to the **implementation of locks** themselves.

The core question of this section is:

> How can we build an efficient lock that provides mutual exclusion at low cost?

To answer this, we must understand the roles of **hardware support** and **operating system support**.

---

### Why Software Alone Is Not Enough

A lock must guarantee **mutual exclusion**:
- At most one thread can be in the critical section at any time.

Ordinary instructions (load, store, add, compare):
- Can be interrupted
- Can be interleaved with other threads
- Are **not atomic**

If two threads try to acquire a lock using only normal instructions, both may observe the lock as free and both may enter the critical section → **race condition**.

Therefore:
- Locks **cannot be implemented correctly using only regular instructions**

---

### Hardware Support: Atomic Instructions

Modern CPUs provide **atomic instructions** that:
- Execute as a single, indivisible operation
- Cannot be interrupted or interleaved
- Work correctly on multiprocessor systems

These instructions form the **foundation of lock implementations**.

Examples (covered later in the chapter):
- Test-and-Set
- Compare-and-Swap (CAS)
- Load-Linked / Store-Conditional (LL/SC)
- Fetch-and-Add

Key idea:
- **Hardware provides atomicity**
- Without atomic instructions, mutual exclusion cannot be guaranteed

---

### Why the Operating System Is Still Needed

Even with atomic hardware instructions, problems remain:
- Busy-waiting (spinning) wastes CPU cycles
- Waiting threads may consume CPU without making progress
- Fairness and scalability are not guaranteed

The OS helps by:
- Putting waiting threads to sleep instead of spinning
- Waking threads when the lock becomes available
- Managing queues of waiting threads
- Integrating lock behavior with scheduling

Thus:
- **Hardware ensures correctness**
- **OS ensures efficiency and scalability**

---

### Big Picture

Building a correct and efficient lock requires:
- Hardware support for atomic operations
- OS support for blocking, waking, and scheduling threads

This section sets the stage for the rest of the chapter, which explores:
- Specific atomic instructions
- Spin locks and their limitations
- How the OS improves lock performance
- How to design practical locking primitives

---

### Exam-Oriented Takeaway

Key facts to remember:
- Locks require **atomic operations**
- Atomicity cannot be achieved with normal instructions
- Hardware provides atomic primitives
- The OS is needed to avoid wasted CPU time and improve fairness

---
---

## 28.4 Evaluating Locks

Before implementing any lock, we must define **what it means for a lock to be good**.  
This section introduces the **three core criteria** used to evaluate lock implementations.

---

### 1. Mutual Exclusion (Correctness)

The most fundamental requirement of a lock is **mutual exclusion**.

Key question:
- Does the lock prevent **more than one thread** from entering the critical section at the same time?

If a lock fails here:
- It is **incorrect**, regardless of how fast or fair it is
- Race conditions are still possible

Exam phrasing to watch for:
- “Does the lock work?”
- “Does it ensure mutual exclusion?”

---

### 2. Fairness

Once correctness is guaranteed, the next concern is **fairness**.

Key questions:
- When the lock becomes free, do waiting threads get a **fair chance** to acquire it?
- Or can some threads be repeatedly bypassed?

Extreme case:
- **Starvation**: a thread waits forever and never acquires the lock

Important notes:
- Fairness is not required for correctness
- But starvation is usually considered unacceptable in real systems

Exam traps:
- A lock can be correct but unfair
- Starvation ≠ deadlock

---

### 3. Performance (Overhead)

The final criterion is **performance**, i.e., how much overhead the lock introduces.

Performance must be evaluated under different scenarios:

#### a) No Contention
- Only one thread acquires and releases the lock
- Question: what is the **baseline cost** of lock/unlock?

#### b) Contention on a Single CPU
- Multiple threads contend for the lock on one CPU
- Issues:
  - Context switching overhead
  - Spinning vs blocking behavior

#### c) Contention on Multiple CPUs
- Threads on different CPUs contend for the same lock
- Issues:
  - Cache coherence traffic
  - Bus contention
  - Scalability problems

By comparing these cases, we can understand:
- When a lock performs well
- When it breaks down

---

### Big Picture

A good lock must balance:
- **Correctness** (mutual exclusion)
- **Fairness** (no starvation)
- **Performance** (low overhead, scalable)

Different lock designs make different trade-offs, which motivates the exploration of:
- Spin locks
- Blocking locks
- Queue-based locks
- Hybrid approaches

---

### Exam-Oriented Summary

Key evaluation criteria for locks:
1. Mutual exclusion (must-have)
2. Fairness (avoid starvation)
3. Performance (overhead under contention)

Always remember:
- Correctness comes first
- Performance must be analyzed under **different contention scenarios**

---
---

## 28.5 Controlling Interrupts

### Basic Idea
One of the earliest approaches to achieve **mutual exclusion** was to **disable interrupts** before entering a critical section and re-enable them afterward.

- `lock()` → disable interrupts  
- `unlock()` → enable interrupts  

On a **single-CPU system**, disabling interrupts prevents the currently running thread from being preempted. As a result, the critical section executes atomically.

---

### Why This Works (in Limited Cases)
- No timer interrupt → no context switch
- The running thread cannot be interrupted
- Guarantees atomic execution **on a single processor**
- Simple and easy to reason about
- Historically used in early operating systems

---

### Why This Is a Bad General Solution

#### 1. Trust and Security Problems
Disabling interrupts is a **privileged operation**.
- A buggy program could disable interrupts and never re-enable them
- A malicious program could monopolize the CPU
- The OS would lose control of the system
- The only recovery may be a system reboot

This violates a core OS principle: **user programs must not be trusted**.

---

#### 2. Does Not Work on Multiprocessors
Disabling interrupts affects **only the local CPU**.

- CPU 0 disables interrupts → protected
- CPU 1 continues running → can still enter the same critical section

Therefore, **mutual exclusion is not guaranteed** on multiprocessor systems.

This alone makes interrupt disabling unsuitable as a general locking mechanism.

---

#### 3. Lost Interrupts
Interrupts signal important events such as:
- Disk I/O completion
- Network packets
- Timer events

If interrupts are disabled for too long:
- Interrupts may be missed
- Waiting processes may never be woken up
- System correctness is compromised

This is a **correctness issue**, not just a performance issue.

---

#### 4. Performance Overhead
Modern CPUs execute interrupt masking and unmasking **slowly**.
- More expensive than normal instructions
- Poor scalability
- Inefficient for frequent locking

---

### Aside: Dekker’s and Peterson’s Algorithms
These are **software-only** mutual exclusion algorithms:
- Use only loads and stores
- No hardware atomic instructions
- Work in theory for two threads

Why they are mostly historical:
- Assume strong memory ordering
- Break on modern CPUs with relaxed memory models
- Busy-waiting is inefficient
- Superseded by hardware-supported atomic operations

Exam note:
- Know what they are
- Know why they are not used in practice
- Do not memorize code unless explicitly required

---

### Key Exam Takeaways
- Disabling interrupts is **not a general locking solution**
- It is acceptable only:
  - Inside the OS kernel
  - For very short critical sections
  - Under single-CPU assumptions
- Modern locks rely on:
  - Atomic hardware instructions
  - OS scheduling support
 
---
---

## 28.6 Test-And-Set (Atomic Exchange)

### The Problem Being Addressed

Disabling interrupts can provide mutual exclusion **only on single-CPU systems**.  
On multiprocessor systems, each CPU has its own interrupt stream, so disabling interrupts on one CPU does **not** prevent other CPUs from entering the same critical section.

Therefore, **hardware support** is required to build correct locks on multiprocessors.

---

### Why a Simple Flag Lock Fails

A naive lock uses a shared flag:

- `flag = 0` → lock is free  
- `flag = 1` → lock is held  

The idea:
1. Check if `flag == 0`
2. Set `flag = 1`
3. Enter critical section

This approach is **incorrect** because checking the flag and setting it are **two separate operations**.

If a context switch or interrupt occurs between these steps:
- Two threads may both see `flag == 0`
- Both set `flag = 1`
- Both enter the critical section

Result: **no mutual exclusion**.

This exact failure is shown by unlucky instruction interleaving.

---

### Test-And-Set: The Key Idea

**Test-and-set** (also called **atomic exchange**) is a special **hardware instruction** that:

- Tests the current value of a memory location
- Sets it to a new value
- Performs both actions **atomically**

Atomic means:
- No interrupts
- No context switches
- No interleaving with other CPUs

Thus:
- Only one thread can observe the lock as free
- All others will see it as already held

This guarantees **mutual exclusion**, the most basic correctness property of a lock.

---

### Correctness vs Performance

Test-and-set **fixes correctness**, but introduces a new issue: **spin-waiting**.

When a thread fails to acquire the lock:
- It repeatedly checks the lock in a loop
- This wastes CPU cycles while doing no useful work

#### Why Spin-Waiting Is Bad

- **Single CPU**: the waiting thread wastes CPU time while the lock holder cannot run
- **Multiple CPUs**: threads burn CPU cycles polling the lock

Thus:
- Correctness: ✅ fixed
- Performance: ❌ inefficient under contention

---

### Key Exam Takeaways

- Simple flag-based locks fail due to non-atomic operations
- Test-and-set provides atomic check-and-set
- Guarantees mutual exclusion
- Causes spin-waiting
- Correct but inefficient under contention
- Motivates more advanced lock designs (yielding, sleeping, queues)

---
---





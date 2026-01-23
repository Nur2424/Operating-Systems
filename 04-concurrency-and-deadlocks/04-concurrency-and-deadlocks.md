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

## 28.7 Building a Working Spin Lock

### Motivation
Previous attempts at building locks failed because they relied on **non-atomic operations**:
- Disabling interrupts works only on **single-CPU systems** and is unsafe.
- Using a simple shared flag (`while(flag==1)`) fails because **checking and setting are separate steps**.

The core problem:
> If the scheduler or another CPU interleaves execution between “check” and “set”, **multiple threads can enter the critical section**.

To fix this, we need **hardware support**.

---

### Hardware Support: Test-and-Set (Atomic Exchange)

Modern CPUs provide an **atomic instruction** commonly called:
- **test-and-set**
- **atomic exchange**

Conceptually, it performs:
- Read a memory location
- Write a new value to it
- Return the old value  
**All in one indivisible (atomic) operation**

Key property:
> No interrupt, context switch, or other CPU can observe an intermediate state.

This atomicity is what allows us to build a **correct lock**.

---

### Idea of a Spin Lock

A spin lock uses:
- A shared variable `flag`
  - `0` → lock is free
  - `1` → lock is held

Lock acquisition:
- Atomically attempt to set `flag = 1`
- If the returned old value was `0`, the thread **acquires the lock**
- If the returned old value was `1`, the thread **keeps trying**

Unlock:
- Simply set `flag = 0`

Because test-and-set is atomic:
> **Only one thread can ever change the flag from 0 to 1**

This guarantees **mutual exclusion**.

---

### Why This Lock Is Correct

This approach fixes the earlier correctness bug because:
- “Test” (is the lock free?)
- “Set” (mark it as held)

are performed **atomically by hardware**.

As a result:
- Two threads cannot both believe they acquired the lock
- Mutual exclusion is guaranteed under any interleaving

---

### Why It Is Called a Spin Lock

If a thread cannot acquire the lock:
- It does **not sleep**
- It repeatedly retries in a loop
- It **spins**, consuming CPU cycles

This leads to performance trade-offs.

---

### Performance Characteristics

#### Single-CPU Systems
Spin locks are usually **inefficient**:
- A spinning thread wastes CPU time **A spinning thread wastes CPU time because it repeatedly checks the lock condition while occupying the CPU instead of sleeping**
- The thread holding the lock may not get scheduled to release it
- Spin locks require a **preemptive scheduler** to even make sense

Without preemption, a spinning thread can block progress forever.

---

#### Multi-CPU Systems
Spin locks can be reasonable when:
- Critical sections are **very short**
- Lock hold time is minimal
- Threads run on different CPUs

However:
- CPU cycles are still wasted while spinning
- Scalability suffers under high contention

---

### Key Exam Takeaways

- Spin locks are the **simplest correct locks**
- They rely on **hardware atomic instructions**
- They guarantee **mutual exclusion**
- They are:
  - Simple
  - Correct
  - Often inefficient

Spin locks are commonly used:
- Inside the **OS kernel**
- For very short critical sections

User-level programs usually prefer:
- Blocking locks (mutexes) that **sleep instead of spin**

---

### Important Mental Model

When reasoning about locks:
> Assume a **malicious scheduler** that interrupts threads at the worst possible time.

If the lock still works under that assumption:
- It is correct

This mindset explains why atomic hardware support is essential for synchronization.

---
---

## 28.8 Evaluating Spin Locks

This section evaluates **spin locks** using three standard criteria for locks:
**correctness**, **fairness**, and **performance**.

---

### 1. Correctness

A spin lock is **correct**.

- It enforces **mutual exclusion**
- At most **one thread** can be in the critical section at any time

From a correctness perspective, the spin lock does its basic job.

> Correctness only asks *“does mutual exclusion hold?”* — not whether the lock is efficient or fair.

---

### 2. Fairness

Spin locks are **not fair**.

- There is **no guarantee** that a waiting thread will ever acquire the lock
- A thread may spin indefinitely while others repeatedly acquire the lock
- **Starvation is possible**

Spin locks do not enforce ordering (e.g., FIFO), and thus provide **no fairness guarantees**.

---

### 3. Performance

Performance depends heavily on the **number of CPUs** and **contention pattern**.

---

#### Case A: Single CPU (Poor Performance)

On a single processor, spin locks perform **very poorly**.

Scenario:
- One thread holds the lock
- It is **preempted while inside the critical section**
- Other threads try to acquire the lock

What happens:
- Each waiting thread **spins for an entire time slice**
- The lock holder is not running, so **no progress is possible**
- CPU time is wasted repeatedly checking the lock

Result:
- Severe waste of CPU cycles
- Very high overhead
- Spin locks are a **bad choice on uniprocessor systems**

---

#### Case B: Multiple CPUs (Acceptable Performance)

On multiprocessor systems, spin locks can work **reasonably well**.

Scenario:
- Thread A holds the lock on CPU 1
- Thread B spins on CPU 2
- Critical section is **short**

What happens:
- Thread A continues running and releases the lock quickly
- Thread B acquires the lock shortly after
- Spinning lasts only briefly

Result:
- Minimal wasted CPU cycles
- Acceptable performance
- Spin locks can be effective **if critical sections are short**

---

### Summary Table

| Criterion     | Spin Lock Behavior |
|---------------|-------------------|
| Correctness   | Correct (mutual exclusion guaranteed) |
| Fairness      | Not fair, starvation possible |
| Performance (1 CPU) | Very poor |
| Performance (many CPUs) | Reasonable if critical sections are short |

---

### Key Exam Takeaway

Spin locks:
- **Guarantee mutual exclusion**
- **Do not guarantee fairness**
- **Waste CPU time on single-CPU systems**
- **Can be effective on multiprocessors with short critical sections**

---
---

## 28.9 Compare-And-Swap (CAS) and Load-Linked / Store-Conditional

### Motivation
Earlier lock attempts using a simple flag failed because **checking** and **setting** the lock were separate operations.  
With unlucky interleavings, multiple threads could enter the critical section at the same time.  
To fix this, we need **hardware support for atomic operations**.

---

## Compare-And-Swap (CAS)

### Core Idea
Compare-and-swap is a **hardware atomic instruction** that:
- Checks whether a memory location has an expected value
- Updates it to a new value **only if** the check succeeds
- Performs both steps **atomically**

This prevents any interleaving between the check and the update.

---

### Conceptual Behavior
CAS takes three inputs:
1. Memory location (e.g., lock flag)
2. Expected value (e.g., `0`)
3. New value (e.g., `1`)

Atomic logic:
- If `*ptr == expected` → write `new`
- Otherwise → do nothing
- Always return the old value

The return value tells the caller whether the operation succeeded.

---

### CAS Used for a Spin Lock
- If the lock is free (`flag == 0`), CAS changes it to `1` and the thread enters the critical section
- If the lock is held (`flag == 1`), CAS fails and the thread keeps spinning
- Unlocking simply resets the flag

**Correctness:**  
Only one thread can successfully change `0 → 1`, guaranteeing mutual exclusion.

---

### Why CAS Is Better Than Test-and-Set
- Test-and-set always writes to memory, even when the lock is held
- CAS writes **only when the lock is actually acquired**
- This reduces unnecessary cache traffic

CAS is powerful enough to build:
- Correct spin locks
- Lock-free data structures

---

### Limitations of CAS Spin Locks
- Still uses **busy waiting**
- No fairness guarantees
- Can waste CPU cycles under contention

CAS fixes **correctness**, not **efficiency**.

---

## Load-Linked / Store-Conditional (LL/SC)

### Motivation
CAS still repeatedly retries blindly.  
LL/SC provides a cleaner way to conditionally update memory.

---

### How LL/SC Works

**Load-Linked (LL):**
- Reads a memory location
- Hardware records that the location was accessed

**Store-Conditional (SC):**
- Attempts to write a new value
- Succeeds **only if no other CPU modified the location** since the LL
- Returns success or failure

If another CPU writes to the location:
- SC fails
- The thread retries

---

### Why LL/SC Is Powerful
- Avoids unnecessary writes
- Scales better on multiprocessors
- Preferred by many RISC architectures

LL/SC is often used as the foundation for advanced synchronization primitives.

---

## Big Picture Comparison

| Mechanism | Mutual Exclusion | Fairness | Performance |
|---------|------------------|----------|-------------|
| Simple flag | ❌ No | ❌ No | ❌ Bad |
| Test-and-set | ✅ Yes | ❌ No | ⚠️ Wasteful |
| CAS | ✅ Yes | ❌ No | ⚠️ Better |
| LL/SC | ✅ Yes | ❌ No | ✅ Better |

---

## Key Exam Takeaways
- **Atomic hardware instructions are required for correct locks**
- CAS and LL/SC fix correctness by preventing interleavings
- Spin locks still waste CPU cycles
- This motivates later designs: sleeping locks, futexes, and hybrid locks

---
---

## 28.10 Load-Linked and Store-Conditional (LL/SC)

### Core Idea
Load-Linked (LL) and Store-Conditional (SC) are **paired hardware instructions** used together to implement **atomic updates** to shared memory.  
They are commonly used to build **locks and other synchronization primitives**, especially on architectures like **MIPS, ARM, PowerPC**.

The key idea is:
- **LL** reads a memory location and marks it as *watched*
- **SC** writes to that location **only if no other write occurred since the LL**

This allows the hardware to detect interference automatically.

---

### Load-Linked (LL)
- Reads a value from memory (like a normal load)
- Internally records that the thread is “linked” to this memory address
- Any intervening write by another thread will invalidate this link

Conceptually:
> “I read this value, and I want to know if anyone else changes it.”

---

### Store-Conditional (SC)
- Attempts to write a new value to the same address used by LL
- Succeeds **only if** no other store occurred since the LL
- Returns:
  - `1` on success (store happened)
  - `0` on failure (store did not happen)

This check + store is performed **atomically by hardware**.

---

### Using LL/SC to Build a Lock (Conceptual)
1. Spin while the lock flag is `1` (lock held)
2. Execute **Load-Linked** on the flag
3. Attempt **Store-Conditional** to set flag to `1`
4. If SC succeeds → lock acquired
5. If SC fails → retry (someone else interfered)

Only **one thread can succeed** in setting the flag.

---

### Why LL/SC Works
- Multiple threads may observe the lock as free
- Only **one SC can succeed**
- Any intervening write causes all other SC attempts to fail
- Ensures **mutual exclusion**

---

### Correctness
- Provides **mutual exclusion**
- Prevents two threads from entering the critical section simultaneously
- Hardware guarantees atomicity

---

### Performance Characteristics

#### Single CPU
- Still a spin lock
- Requires a **preemptive scheduler**
- If lock holder is preempted, other threads may waste CPU spinning

#### Multiple CPUs
- Performs well when:
  - Critical sections are short
  - Number of threads ≈ number of CPUs
- Spinning on another CPU is often acceptable
- More scalable than naive spin locks

---

### LL/SC vs Test-and-Set
| Aspect | Test-and-Set | LL/SC |
|------|--------------|-------|
| Writes while spinning | Yes | No |
| Cache contention | High | Lower |
| Scalability | Poor | Better |
| Conflict detection | Software-visible | Hardware-enforced |

LL/SC avoids unnecessary writes during spinning, reducing bus and cache traffic.

---

### Failure Scenario (Important)
- Two threads execute LL and see the lock as free
- Thread A executes SC → succeeds
- Thread B executes SC → fails
- Thread B retries

This behavior is **expected and correct**.

---

### Exam Takeaways
- LL/SC is a **hardware-supported atomic mechanism**
- SC succeeds only if **no intervening store** occurred
- Used to build:
  - Locks
  - Atomic variables
  - Lock-free data structures
- More scalable than test-and-set
- Still forms a **spin lock** when used this way

---
---

## 28.11 Fetch-And-Add

### What is Fetch-and-Add?
Fetch-and-add is a **hardware atomic instruction** that:
- **Atomically increments** a memory value
- **Returns the old value** that was stored before the increment

The key point is **atomicity**: no other thread can interleave between the read and the update.

---

### Why Fetch-and-Add is Useful
Unlike simple test-and-set spin locks, fetch-and-add enables:
- **Ordering**
- **Fairness**
- **Guaranteed progress**

This makes it possible to build stronger synchronization primitives than basic spin locks.

---

### Ticket Lock: Core Idea
Fetch-and-add is commonly used to implement a **ticket lock**.

A ticket lock uses **two shared variables**:
- `ticket`: assigns an increasing ticket number to arriving threads
- `turn`: indicates which ticket number is currently allowed to enter the critical section

Each thread:
1. Atomically gets a ticket number using fetch-and-add
2. Spins until `turn == myticket`
3. Enters the critical section
4. Increments `turn` on unlock to wake the next thread

---

### How Ticket Locks Work (Step-by-Step)

#### Lock acquisition
- Thread performs `FetchAndAdd(ticket)`
- Returned value becomes `myturn`
- Thread spins while `turn != myturn`

#### Lock release
- Thread increments `turn`
- Next waiting thread can now enter

---

### Why Ticket Locks Are Better Than Test-and-Set

#### Fairness
- Threads enter the critical section **in arrival order**
- No starvation
- Every thread is guaranteed to make progress

#### Progress guarantee
- Once a thread gets a ticket, it **will eventually enter**
- This is not true for test-and-set locks, where threads can spin forever

---

### Performance Characteristics

#### Pros
- Fair
- Starvation-free
- Simple conceptually

#### Cons
- Still a **spin lock** → wastes CPU cycles while waiting
- On multiprocessors, spinning causes cache-line contention on `turn`
- Does not sleep waiting threads (unlike mutexes)

---

### Comparison Summary

| Lock Type        | Fairness | Starvation | CPU Waste | Typical Use |
|------------------|----------|------------|-----------|-------------|
| Test-and-Set     | No       | Possible   | High      | Simple locks |
| Compare-and-Swap | No       | Possible   | High      | Lock-free algorithms |
| Ticket Lock      | Yes      | No         | Medium    | Short critical sections |

---

### Exam-Focused Takeaways
- Fetch-and-add is **atomic read + increment**
- Ticket locks use fetch-and-add to assign **order**
- Ticket locks guarantee **fairness and progress**
- Still inefficient for long critical sections
- Better than basic spin locks, worse than sleeping locks

---

### One-Line Exam Answer
> Fetch-and-add enables ticket locks, which provide fairness and prevent starvation by serving threads in strict arrival order.

---
---

## 28.12 Too Much Spinning: What Now?

### Problem Recap
- **Spin locks work** (mutual exclusion is correct).
- But on a **single CPU**, they can be **very inefficient**.

### Why Spinning Is Bad (Single CPU)
- Scenario:
  - Thread T0 holds the lock and is **preempted** inside the critical section.
  - Thread T1 runs, tries to acquire the lock, finds it held → **spins**.
- Result:
  - T1 **wastes an entire time slice** repeatedly checking a value that **cannot change** until T0 runs again.
  - If **N threads** contend for the lock:
    - **N − 1 time slices** can be completely wasted spinning.

### Key Insight
- Spinning assumes the lock holder will **run soon**.
- On a single CPU, that assumption is often **false** because of preemption.
- More contenders ⇒ more wasted CPU time.

### Core Problem
> Busy-waiting (spinning) wastes CPU cycles when the lock holder cannot run.

### The Crux
**How can we build a lock that does NOT waste CPU time spinning?**

### Answer (High-Level)
- **Hardware support alone is not enough**.
- We need **OS support** so that:
  - Threads **sleep** when the lock is held.
  - Threads are **woken up** when the lock is released.

### Direction Forward
- Replace *spin* with *block*:
  - Instead of spinning → **put the thread to sleep**
  - Wake it up when the lock becomes available
- Leads to:
  - Sleeping locks
  - Queues
  - OS-managed synchronization

### Exam Takeaways
- Spin locks:
  - Good on **multiple CPUs** with short critical sections
  - Bad on **single CPU** under contention
- Excessive spinning:
  - Wastes time slices
  - Scales poorly with number of threads
- Motivation for OS-assisted locks (sleep/wakeup)

---
---

## 28.13 A Simple Approach: Just Yield, Baby

### Motivation
Spin locks waste CPU time when a thread holding the lock is preempted, especially on a **single CPU**.  
Instead of continuously spinning, a waiting thread can **voluntarily give up the CPU** and let another thread run.

---

### Core Idea
When a thread tries to acquire a lock and finds it already held:
- Instead of spinning, it calls `yield()`
- `yield()` is a **system call**
- The thread gives up the CPU voluntarily

This replaces **busy waiting** with **cooperative scheduling**.

---

### What `yield()` Does (Exam-Critical)
- Moves the thread from **RUNNING → READY**
- Does **not block** the thread
- The thread remains runnable but is not currently executing
- The scheduler is free to run another thread

Important distinction:
- `yield()` ≠ sleep
- `yield()` ≠ block
- `yield()` ≠ spin

---

### How the Yield-Based Lock Works
- Thread calls `lock()`
- If lock is free → acquire it
- If lock is held → call `yield()` repeatedly until lock becomes free
- Once the thread runs again and sees the lock free → acquires it

---

### Performance Analysis

#### Case 1: Two Threads, One CPU (Best Case)
- Thread A holds the lock
- Thread B tries to acquire → calls `yield()`
- Scheduler runs Thread A
- Thread A releases the lock
- Thread B later acquires it

Result:
- No busy waiting
- Minimal wasted CPU
- Much better than spin locks

---

#### Case 2: Many Threads Contending (Problematic)
- One thread holds the lock but is preempted
- Many other threads:
  - Run
  - See lock held
  - Call `yield()`
  - Cause context switches

Result:
- CPU cycles saved compared to spinning
- **High context-switch overhead**
- Still significant performance cost

---

### Fairness and Starvation
Yield-based locks **do not guarantee fairness**.

A thread may:
- Repeatedly yield
- Never acquire the lock
- Starve while other threads repeatedly acquire and release the lock

Key point:
- Yield reduces CPU waste
- Yield does **not** prevent starvation

---

### Comparison Summary

| Approach        | CPU Waste | Context Switch Cost | Fairness | Starvation |
|----------------|-----------|---------------------|----------|------------|
| Spin Lock      | High      | Low                 | No       | Yes        |
| Yield Lock     | Lower     | High                | No       | Yes        |

---

### Exam Takeaways
- Yield-based locks replace spinning with voluntary descheduling
- `yield()` moves a thread from RUNNING to READY
- Yield reduces wasted CPU cycles but increases context-switch overhead
- Yield-based locks are **not fair**
- Starvation is still possible

---
---

## 28.14 Using Queues: Sleeping Instead of Spinning

### Problem Recap: Why Spinning & Yielding Are Not Enough
Earlier approaches (pure spinning, or spinning + yield) still suffer from:
- **Wasted CPU time** (spinning checks a value repeatedly)
- **Scheduler dependence** (bad scheduling decisions → wasted time)
- **Starvation** (no guarantee of who gets the lock next)

Key issue: **we leave too much to chance**.  
The scheduler decides who runs next, not the lock.

---

### Core Idea
Instead of:
- spinning on the CPU, or
- repeatedly yielding,

👉 **put waiting threads to sleep** and **explicitly wake them up** when the lock becomes available.

This requires:
- **OS support**
- **A queue of waiting threads**

---

### OS Primitives Used
We assume two OS-provided primitives:

- `park()`  
  → puts the calling thread to sleep (blocked state)

- `unpark(threadID)`  
  → wakes up a specific sleeping thread

These allow the lock to:
- block threads instead of wasting CPU
- precisely control who gets the lock next

---

### High-Level Lock Structure
The lock maintains:
- `flag` → whether the lock is held (0 = free, 1 = held)
- `guard` → a **small spin lock** protecting internal lock data
- `queue` → list of thread IDs waiting for the lock

Important:  
The **guard** is only held for a *very short time* to protect the queue and flag.

---

### Lock Acquisition Logic (Conceptual)
1. Acquire `guard` using test-and-set (short spin)
2. If lock is free:
   - Set `flag = 1`
   - Release `guard`
   - Enter critical section
3. If lock is held:
   - Add current thread to the waiting queue
   - Release `guard`
   - Call `park()` → thread sleeps

---

### Lock Release Logic (Conceptual)
1. Acquire `guard`
2. If queue is empty:
   - Set `flag = 0` (lock becomes free)
3. If queue is not empty:
   - Remove one waiting thread from queue
   - Wake it up using `unpark(threadID)`
   - **Directly transfer ownership of the lock**
     (flag is NOT set to 0 in between)
4. Release `guard`

---

### Why This Is Better

#### Compared to Spinning
- No long busy-wait loops
- CPU cycles are not wasted checking a flag

#### Compared to Yielding
- No repeated context-switch storms
- No dependence on scheduler fairness

#### Fairness
- Queue enforces **FIFO-like behavior**
- Prevents starvation

---

### Remaining Spin-Waiting (Why It’s Acceptable)
This approach **does not eliminate spinning entirely**:
- Threads spin briefly while acquiring `guard`

But:
- Spin duration is **very short**
- Only protects lock metadata, not user critical sections
- Acceptable tradeoff

---

### Subtle Bug: Wakeup / Waiting Race
There is a dangerous race:
- A thread is **about to call `park()`**
- Another thread releases the lock and calls `unpark()`
- If `park()` happens *after* `unpark()`, the wakeup is lost
- Thread sleeps forever

This is called the **wakeup/waiting race**

---

### Fix: `setpark()`
To solve this, Solaris adds:
- `setpark()` → marks a thread as *about to sleep*

If `unpark()` happens before `park()`:
- `park()` returns immediately
- No lost wakeup

This makes sleeping locks **correct**.

---

### Key Exam Takeaways
- Spinning wastes CPU cycles
- Yielding reduces spinning but causes many context switches
- Sleeping locks:
  - require OS support
  - use queues for fairness
  - minimize wasted CPU
- Guard spin-lock protects lock internals
- Wakeup/waiting race is a classic concurrency bug
- `setpark()` is used to prevent lost wakeups

---

### Mental Model (Very Important)
> **Spinning = hoping the scheduler helps you**  
> **Sleeping = telling the OS exactly what you want**

Sleeping locks give **control**, not hope. 

---
---

## 28.15 — Different OS, Different Support (Futex)

### Big Picture
User-level locks built only with hardware primitives (spin locks, CAS, LL/SC) are **not enough** for real systems.  
They either:
- waste CPU cycles (spinning), or
- cause too many context switches (yielding).

Modern operating systems provide **special OS support** to build **efficient, scalable locks**.

Linux’s solution is the **futex**.

---

### What is a Futex?
**Futex = Fast Userspace Mutex**

A futex is:
- a **user-space integer**
- plus an **associated kernel wait queue** (used only when needed)

Key design goal:
> **Avoid kernel involvement unless there is contention.**

---

### How Futex-Based Locks Work

#### Fast Path (No Contention)
- Thread tries to acquire the lock using an **atomic instruction**
- If the lock is free:
  - acquisition succeeds
  - **no system call**
  - **no kernel involvement**

This is extremely fast and is the common case.

---

#### Slow Path (Contention)
- If the lock is already held:
  - thread calls `futex_wait(address, expected)`
  - kernel puts the thread to sleep
- When the lock is released:
  - unlocking thread calls `futex_wake(address)`
  - kernel wakes **one waiting thread**

Result:
- no busy-waiting
- no wasted CPU time
- controlled wakeup

---

### Why Futexes Are Better Than Spinning

| Approach | Problem |
|--------|--------|
| Spin lock | Wastes CPU cycles |
| Yield-based lock | Many context switches |
| Futex | Sleeps only when needed |

Futex combines:
- **hardware atomics** (fast path)
- **kernel sleep/wakeup** (slow path)

---

### Key Properties (Exam-Relevant)

#### 1. Futex is NOT a lock
- Futex is a **mechanism**, not a lock
- Lock logic lives in **user space**
- Kernel only assists with sleep/wakeup

---

#### 2. Kernel is involved only on contention
Common trap:
- “Futex always uses system calls” → **False**

Kernel calls happen only when:
- a thread must sleep
- a thread must be woken

---

#### 3. Futex prevents lost wakeups
`futex_wait(addr, expected)`:
- thread sleeps **only if** `*addr == expected`
- otherwise returns immediately

This avoids the classic **wakeup/waiting race**.

---

#### 4. Optimized for the common case
Linux mutexes are fast because:
- uncontended case = one atomic instruction
- no kernel data structures touched

---

### Why “Different OS, Different Support” Matters
All operating systems need:
- mutual exclusion
- fairness
- performance

But they provide **different primitives**:
- Solaris → `park()` / `unpark()`
- Linux → `futex`
- Others → different kernel mechanisms

Lock design is a **cooperation** between:
- hardware
- operating system
- user-level libraries

---

### Common Exam Traps
- Futex is a kernel lock → ❌
- Futex always blocks → ❌
- Futex replaces hardware atomics → ❌
- Futex is the same as a spin lock → ❌

---

### One-Sentence Exam Summary
**Linux futexes allow locks to run entirely in user space when uncontended and use the kernel only to sleep and wake threads under contention, achieving both performance and scalability.**

---
---

## 28.16 — Two-Phase Locks

### Core Idea
A **two-phase lock** combines **spinning** and **sleeping** into a single locking strategy.

Key insight:
> Spinning is sometimes good, but not forever.

This approach has existed for decades (e.g., **Dahm locks, 1960s**) and is still used today in modern OSes like Linux.

---

### Why Two Phases?
Pure approaches have weaknesses:

- **Pure spinning**
  - wastes CPU cycles
  - terrible under long lock holds
- **Pure sleeping**
  - incurs context switch overhead
  - slow when the lock will be released very soon

Two-phase locks try to **get the best of both worlds**.

---

### Phase 1 — Spin (Optimistic Phase)
- When a thread tries to acquire a lock:
  - it **spins for a short time**
  - hoping the lock holder will release it quickly

This works well when:
- critical sections are short
- lock is about to be released
- spinning avoids expensive sleep/wakeup

---

### Phase 2 — Sleep (Fallback Phase)
- If the lock is **not acquired during spinning**:
  - the thread **goes to sleep**
  - OS (e.g., futex) wakes it later

This prevents:
- unbounded spinning
- wasting full time slices
- starvation of other threads

---

### Linux Example
Linux futex-based locks already behave like a **two-phase lock**:

- Phase 1: try atomic operation once (or spin briefly)
- Phase 2: call `futex_wait()` and sleep

A more general two-phase lock might:
- spin for *N iterations*
- then fall back to futex-based sleeping

---

### Why This Is a Hybrid Approach
Two-phase locks are **hybrid**:
- hardware support (atomic ops, cache coherence)
- OS support (sleep/wakeup)
- user-space logic (lock policy)

They adapt to:
- workload
- contention level
- number of CPUs
- length of critical sections

---

### Tradeoffs (Exam-Focused)

| Aspect | Two-Phase Lock |
|-----|---------------|
| Correctness | Yes |
| Performance | Good (adaptive) |
| Fairness | Depends on implementation |
| Complexity | Higher than simple spin lock |

There is **no single best lock** for all situations.

---

### Common Exam Traps
- Two-phase locks never spin → ❌
- Two-phase locks are purely OS-based → ❌
- Two-phase locks eliminate contention → ❌
- Two-phase locks always outperform spin locks → ❌

---

### One-Sentence Exam Summary
**Two-phase locks first spin briefly hoping for quick lock release and, if that fails, put the thread to sleep using OS support, balancing performance and CPU efficiency.**

---
---

## STEP 1 — Conceptual, exam-oriented summary (plain text, no Markdown)

This chapter answers **one core exam question** in many disguises:

> *How does an operating system (with hardware help) provide mutual exclusion efficiently, fairly, and correctly under concurrency?*

### Big Picture (what examiners care about)
Locks exist because **concurrent threads cannot safely execute critical sections without coordination**. Even a single instruction like `x = x + 1` is *not atomic* and can interleave.

The chapter walks through **why naïve solutions fail**, and **how real OSes fix them**, step by step.

---

### 1. What a Lock Really Is
A lock is **state + rules**:
- state: free / held
- rule: at most **one thread** can hold it at a time

Correctness requirement:
- **mutual exclusion** (never two threads in the critical section)

This connects directly to **race conditions** from the concurrency chapter and **critical sections** from earlier exams.

---

### 2. Why Simple Ideas Fail
Several “obvious” ideas appear in exams as **traps**:

- **Disable interrupts**
  - Works only on single CPU
  - Unsafe for user programs
  - Breaks I/O, timers, responsiveness
  - Modern systems: unacceptable

- **Simple flag variable**
  - Fails due to interleavings
  - Two threads can both see `flag == 0`
  - Violates mutual exclusion

These failures teach an exam pattern:
> If a solution depends on *non-atomic* reads/writes → it is broken.

---

### 3. Hardware Support Is Mandatory
To build correct locks, **atomic instructions are required**.

Key ones you must recognize in exams:
- **Test-and-Set (atomic exchange)**
- **Compare-and-Swap (CAS)**
- **Load-Linked / Store-Conditional (LL/SC)**
- **Fetch-and-Add**

All of them guarantee:
> *The check + update happens as one indivisible operation.*

This connects directly to:
- CPU architecture
- cache coherence
- atomicity assumptions in concurrency questions

---

### 4. Spin Locks: Correct but Dangerous
Spin locks:
- are **correct**
- provide **mutual exclusion**
- but **waste CPU cycles**

Exam-critical distinction:
- **Single CPU** → spin locks are terrible
- **Multiple CPUs** → spin locks may be acceptable for *short* critical sections

Problems examiners love:
- starvation
- unfairness
- wasted time slices
- preemption inside critical sections

---

### 5. Fairness: Ticket Locks
Using **fetch-and-add**, ticket locks:
- assign each thread a number
- serve threads in order
- guarantee **progress**
- prevent starvation

This links directly to:
- fairness in scheduling (RR, MLFQ)
- starvation questions
- queue-based reasoning

Exam insight:
> Ticket locks trade performance for fairness.

---

### 6. Too Much Spinning → OS Must Help
Spinning alone is not enough.

Key idea:
> Hardware solves *atomicity*, not *efficiency*.

If a lock holder is descheduled:
- all waiting threads spin uselessly
- entire time slices are wasted

This motivates **sleeping instead of spinning**.

---

### 7. Yield-Based Locks (Halfway Fix)
Replacing spinning with `yield()`:
- reduces CPU waste
- still causes many context switches
- still allows starvation

Exam takeaway:
> Yield helps, but does not solve scheduling or fairness.

---

### 8. Queue-Based Locks: Sleeping Instead of Spinning
Using OS primitives:
- threads **sleep** when lock is busy
- threads are **woken explicitly**
- a **queue** determines order

Important mechanisms:
- `park()` / `unpark()`
- `setpark()` to avoid wakeup races

This directly connects to:
- condition variables
- scheduler queues
- blocking vs busy waiting (frequent exam topic)

---

### 9. Real OS Support: Futex (Linux)
Linux uses **futexes**:
- fast path: user-space atomic ops
- slow path: kernel sleep/wakeup
- hybrid user/kernel solution

Exam gold:
> Futex = “Fast Userspace Mutex”

You should recognize:
- why kernel is involved only on contention
- why this scales well
- why pure user-space locks are insufficient

---

### 10. Two-Phase Locks: The Modern Compromise
Two-phase locks:
1. spin briefly
2. sleep if necessary

Why this matters:
- short critical sections → spinning is efficient
- long waits → sleeping avoids waste

This connects to:
- multiprocessor scheduling
- hybrid designs in OS
- tradeoffs between latency and throughput

---

### Final Exam Mindset for This Chapter
Examiners are **not** testing syntax or code.

They test:
- can you reason about **interleavings**
- can you spot **broken atomicity**
- can you explain **why spinning is bad**
- can you connect **hardware + OS + scheduler**
- can you justify **design tradeoffs**

If you understand:
- *why* each solution was invented
- *what problem it fixes*
- *what new problem it introduces*

Then you are **exam-ready** for this chapter.

---
---





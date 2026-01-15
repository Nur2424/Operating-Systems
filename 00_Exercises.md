### Q1) Machine state (Ch. 4)

The OS must save/restore machine state primarily to:
A. Reduce page faults
B. Enable context switching so a process resumes correctly
C. Improve disk throughput
D. Avoid internal fragmentation

#### B We save/restore machine state to context switch and later resume a process correctly (PC, registers, memory context, etc.)

---

### Q2) Process state transition (Ch. 4)

A running process most directly transitions to blocked/waiting when:
A. Its time slice ends
B. Another process terminates
C. It issues an I/O request that cannot complete immediately
D. It calls a normal (user-space) function

#### C  A process becomes blocked/waiting when it cannot continue and must wait for an event, typically I/O.
	•	A (time slice ends) → Running → Ready (preemption), not waiting
	•	B (another process terminates) → doesn’t block you
	•	D (normal function call) → stays running (no OS waiting)

Rule to remember:
	•	Time slice ends → READY
	•	I/O request → BLOCKED/WAITING

---

### Q3) PCB purpose (Ch. 4)

A Process Control Block (PCB) is best described as:
A. A CPU register used for scheduling
B. The kernel data structure that stores per-process state (e.g., registers, state, memory info)
C. A user-space library used to create threads
D. A page table entry for a process

#### B PCB is the kernel structure holding per-process state (state, registers, memory info, PID, etc.).

---

### Q4) fork() return values (Ch. 5)

After a successful fork(), which statement is correct?
A. Both parent and child see return value 0
B. Parent sees child PID; child sees 0
C. Child sees parent PID; parent sees 0
D. Both parent and child see child PID

#### B Parent gets child PID, child gets 0.

---

### Q5) exec() semantics (Ch. 5)

If exec() succeeds, then:
A. It creates a new process with a new PID
B. It replaces the current process’s address space and does not return
C. It blocks until the new program finishes
D. It returns twice (once in parent, once in child)

#### B Exec replaces address space and does not return on success. PID stays the same.

---

#### Q6) wait() and zombies (Ch. 5)

A zombie process exists because:
A. It is still executing on the CPU
B. It is blocked waiting for I/O
C. It has exited but its parent has not yet collected its exit status
D. It has been swapped out to disk

#### C. It has exited but its parent has not yet collected its exit status

---

#### Q7) Threads share what? (Ch. 26)

Threads of the same process share:
A. The same stack
B. The same registers
C. The same address space (e.g., heap + globals)
D. The same program counter

#### C Threads share address space (globals + heap), but not registers/PC/stack.

---

#### Q8) Race condition definition (Ch. 26)

A race condition requires:
A. Only one thread and a shared variable
B. Multiple threads, shared data, and at least one write, with outcome depending on timing
C. Multiple processes and no shared memory
D. A deadlock and a page fault at the same time

#### B Shared data + at least one write + outcome depends on timing/interleaving.

---

#### Q9) Why “counter++” is unsafe (Ch. 26)

The core reason counter = counter + 1 can produce wrong results under concurrency is:
A. It always causes a page fault
B. It is not atomic; it expands into multiple instructions that can interleave
C. The compiler refuses to optimize it
D. It is only unsafe on multi-core CPUs

#### B It’s multiple instructions (load/add/store) → can interleave → lost update.

---

### Q10) pthread_join meaning (Ch. 27)

pthread_join(t, …) is best described as:
A. It forces thread t to start executing immediately
B. It blocks the caller until thread t finishes
C. It terminates thread t
D. It converts a user thread into a kernel thread

#### B Blocks caller until thread finishes; like wait() but for threads.

Memorize this transition table:

	•	Running → Ready = time slice ends / preemption
	•	Running → Blocked (Waiting) = I/O request / waiting for event
	•	Blocked → Ready = I/O completes (interrupt wakes it)

---
---

### Q1) User → kernel mode (Ch. 4 / OS basics)

A transition from user mode to kernel mode occurs when:
A. A program calls a normal function  
B. The system boots  
C. A system call or interrupt/trap occurs  
D. The compiler optimizes code  

#### C  
System calls, traps, and interrupts cause the CPU to switch from user mode to kernel mode in order to execute privileged operations.

---

### Q2) fork() sharing (Ch. 5)

After `fork()`, what is definitely **not shared** between parent and child?
A. PID  
B. Code (initial contents)  
C. Open file descriptions (conceptually inherited)  
D. None of the above  

#### A  
Parent and child processes always have different PIDs.  
Code and open files are inherited conceptually, but process identifiers are unique.

---

### Q3) exec() + PID (Ch. 5)

After a successful `exec()`:
A. PID changes  
B. PID stays the same  
C. Parent PID changes  
D. The process becomes a zombie  

#### B  
`exec()` replaces the current process’s address space but does not create a new process.  
The PID remains unchanged.

---

### Q4) wait() effect on parent (Ch. 5)

When a parent calls `wait()` and the child is still running, the parent becomes:
A. Ready  
B. Running  
C. Blocked  
D. Terminated  

#### C  
If the child has not yet terminated, `wait()` causes the parent to block until the child exits.

---

### Q5) Threads vs processes (Ch. 26)

Which is true?
A. Threads have separate address spaces  
B. Threads share address space but each has its own stack  
C. Threads share stacks but have different heaps  
D. Threads always run in parallel  

#### B  
Threads of the same process share the address space (heap + globals) but each thread has its own stack.

---

### Q6) Critical section (Ch. 26)

A critical section is:
A. Code that always triggers interrupts  
B. Code that accesses shared data and must be executed with mutual exclusion  
C. A system call handler routine  
D. A process state stored in PCB  

#### B  
A critical section is a portion of code that accesses shared data and must not be executed concurrently by multiple threads.

---

### Q7) Mutual exclusion goal (Ch. 26)

Mutual exclusion ensures:
A. Multiple threads can enter a critical section for performance  
B. Only one thread executes a critical section at a time  
C. Threads never context switch  
D. Deadlocks cannot occur  

#### B  
Mutual exclusion guarantees that at most one thread executes a critical section at any given time.

---

### Q8) Interleaving computation (Ch. 26)

`val` starts at 1 in memory.

Thread P:  
P1: R1 = val  
P2: R1 += 1  
P3: val = R1  

Thread C:  
C1: R2 = val  
C2: R2 += 1  
C3: val = R2  

Schedule: **C1, C2, P1, P2, P3, C3**

Final value in `val` is:
A. 1  
B. 2  
C. 3  
D. Cannot be determined  

#### B  

Step-by-step execution:
- Initial: `val = 1`
- C1: R2 = 1
- C2: R2 = 2
- P1: R1 = 1
- P2: R1 = 2
- P3: val = 2
- C3: val = 2

Final value stored in memory is **2**.

Key rule:
- Registers are private to each thread
- Shared memory writes determine the final result
- The last write to memory wins

---

### Q9) pthread_cond_wait behavior (Ch. 27)

`pthread_cond_wait(cond, lock)` conceptually:
A. Sleeps while holding the lock forever  
B. Sleeps and releases the lock, then re-acquires it before returning  
C. Releases the lock and never sleeps  
D. Terminates the calling thread  

#### B  

Correct behavior of `pthread_cond_wait`:
- Atomically releases the associated lock
- Puts the thread to sleep
- Re-acquires the lock before returning

This allows another thread to acquire the lock, update shared state, and signal the condition.

---

### Q10) Condition variables (Ch. 27)

Condition variables are mainly used to:
A. Prevent context switches  
B. Avoid busy-waiting by sleeping until a condition becomes true  
C. Replace `fork()`  
D. Increase page size  

#### B  
Condition variables allow threads to sleep efficiently while waiting for a condition, avoiding CPU waste due to busy-waiting.

---



## Q1) Machine state (Ch. 4)

The OS must save/restore machine state primarily to:  
A. Reduce page faults  
B. Enable context switching so a process resumes correctly  
C. Improve disk throughput  
D. Avoid internal fragmentation  

**Answer: B**  
We save/restore machine state to context switch and later resume a process correctly (PC, registers, memory context, etc.).

---

## Q2) Process state transition (Ch. 4)

A running process most directly transitions to blocked/waiting when:  
A. Its time slice ends  
B. Another process terminates  
C. It issues an I/O request that cannot complete immediately  
D. It calls a normal (user-space) function  

**Answer: C**  
A process becomes blocked/waiting when it cannot continue and must wait for an event, typically I/O.

- A (time slice ends) → Running → Ready (preemption), not waiting  
- B (another process terminates) → doesn’t block you  
- D (normal function call) → stays running (no OS waiting)  

**Rule to remember:**
- Running → Ready = time slice ends / preemption  
- Running → Blocked (Waiting) = I/O request  

---

## Q3) PCB purpose (Ch. 4)

A Process Control Block (PCB) is best described as:  
A. A CPU register used for scheduling  
B. The kernel data structure that stores per-process state (e.g., registers, state, memory info)  
C. A user-space library used to create threads  
D. A page table entry for a process  

**Answer: B**  
PCB is the kernel structure holding per-process state (state, registers, memory info, PID, etc.).

---

## Q4) fork() return values (Ch. 5)

After a successful fork(), which statement is correct?  
A. Both parent and child see return value 0  
B. Parent sees child PID; child sees 0  
C. Child sees parent PID; parent sees 0  
D. Both parent and child see child PID  

**Answer: B**  
Parent gets child PID, child gets 0.

---

## Q5) exec() semantics (Ch. 5)

If exec() succeeds, then:  
A. It creates a new process with a new PID  
B. It replaces the current process’s address space and does not return  
C. It blocks until the new program finishes  
D. It returns twice (once in parent, once in child)  

**Answer: B**  
Exec replaces address space and does not return on success. PID stays the same.

---

## Q6) wait() and zombies (Ch. 5)

A zombie process exists because:  
A. It is still executing on the CPU  
B. It is blocked waiting for I/O  
C. It has exited but its parent has not yet collected its exit status  
D. It has been swapped out to disk  

**Answer: C**  
It has exited but its parent has not yet collected its exit status.

---

## Q7) Threads share what? (Ch. 26)

Threads of the same process share:  
A. The same stack  
B. The same registers  
C. The same address space (e.g., heap + globals)  
D. The same program counter  

**Answer: C**  
Threads share address space (globals + heap), but not registers, PC, or stack.

---

## Q8) Race condition definition (Ch. 26)

A race condition requires:  
A. Only one thread and a shared variable  
B. Multiple threads, shared data, and at least one write, with outcome depending on timing  
C. Multiple processes and no shared memory  
D. A deadlock and a page fault at the same time  

**Answer: B**  
Shared data + at least one write + outcome depends on timing/interleaving.

---

## Q9) Why “counter++” is unsafe (Ch. 26)

The core reason `counter = counter + 1` can produce wrong results under concurrency is:  
A. It always causes a page fault  
B. It is not atomic; it expands into multiple instructions that can interleave  
C. The compiler refuses to optimize it  
D. It is only unsafe on multi-core CPUs  

**Answer: B**  
It’s multiple instructions (load/add/store) → can interleave → lost update.

---

## Q10) pthread_join meaning (Ch. 27)

`pthread_join(t, …)` is best described as:  
A. It forces thread t to start executing immediately  
B. It blocks the caller until thread t finishes  
C. It terminates thread t  
D. It converts a user thread into a kernel thread  

**Answer: B**  
Blocks caller until thread finishes; like wait() but for threads.

---

---

## Q1) User → kernel mode (Ch. 4 / OS basics)

A transition from user mode to kernel mode occurs when:  
A. A program calls a normal function  
B. The system boots  
C. A system call or interrupt/trap occurs  
D. The compiler optimizes code  

**Answer: C**  
System calls, traps, and interrupts cause the CPU to switch from user mode to kernel mode in order to execute privileged operations.

---

## Q2) fork() sharing (Ch. 5)

After `fork()`, what is definitely **not shared** between parent and child?  
A. PID  
B. Code (initial contents)  
C. Open file descriptions (conceptually inherited)  
D. None of the above  

**Answer: A**  
Parent and child processes always have different PIDs.  
Code and open files are inherited conceptually, but process identifiers are unique.

---

## Q3) exec() + PID (Ch. 5)

After a successful `exec()`:  
A. PID changes  
B. PID stays the same  
C. Parent PID changes  
D. The process becomes a zombie  

**Answer: B**  
`exec()` replaces the current process’s address space but does not create a new process.  
The PID remains unchanged.

---

## Q4) wait() effect on parent (Ch. 5)

When a parent calls `wait()` and the child is still running, the parent becomes:  
A. Ready  
B. Running  
C. Blocked  
D. Terminated  

**Answer: C**  
If the child has not yet terminated, `wait()` causes the parent to block until the child exits.

---

## Q5) Threads vs processes (Ch. 26)

Which is true?  
A. Threads have separate address spaces  
B. Threads share address space but each has its own stack  
C. Threads share stacks but have different heaps  
D. Threads always run in parallel  

**Answer: B**  
Threads of the same process share the address space (heap + globals) but each thread has its own stack.

---

## Q6) Critical section (Ch. 26)

A critical section is:  
A. Code that always triggers interrupts  
B. Code that accesses shared data and must be executed with mutual exclusion  
C. A system call handler routine  
D. A process state stored in PCB  

**Answer: B**  
A critical section is a portion of code that accesses shared data and must not be executed concurrently by multiple threads.

---

## Q7) Mutual exclusion goal (Ch. 26)

Mutual exclusion ensures:  
A. Multiple threads can enter a critical section for performance  
B. Only one thread executes a critical section at a time  
C. Threads never context switch  
D. Deadlocks cannot occur  

**Answer: B**  
Mutual exclusion guarantees that at most one thread executes a critical section at any given time.

---

## Q8) Interleaving computation (Ch. 26)

`val` starts at 1 in memory.

**Thread P:**  
P1: R1 = val  
P2: R1 += 1  
P3: val = R1  

**Thread C:**  
C1: R2 = val  
C2: R2 += 1  
C3: val = R2  

Schedule: **C1, C2, P1, P2, P3, C3**

Final value in `val` is:  
A. 1  
B. 2  
C. 3  
D. Cannot be determined  

**Answer: B**

Step-by-step execution:
- Initial: `val = 1`
- C1: R2 = 1
- C2: R2 = 2
- P1: R1 = 1
- P2: R1 = 2
- P3: val = 2
- C3: val = 2

Final value stored in memory is **2**.

**Key rule:**
- Registers are private to each thread  
- Shared memory writes determine the final result  
- The last write to memory wins  

---

## Q9) pthread_cond_wait behavior (Ch. 27)

`pthread_cond_wait(cond, lock)` conceptually:  
A. Sleeps while holding the lock forever  
B. Sleeps and releases the lock, then re-acquires it before returning  
C. Releases the lock and never sleeps  
D. Terminates the calling thread  

**Answer: B**

Correct behavior of `pthread_cond_wait`:
- Atomically releases the associated lock  
- Puts the thread to sleep  
- Re-acquires the lock before returning  

This allows another thread to acquire the lock, update shared state, and signal the condition.

---

## Q10) Condition variables (Ch. 27)

Condition variables are mainly used to:  
A. Prevent context switches  
B. Avoid busy-waiting by sleeping until a condition becomes true  
C. Replace `fork()`  
D. Increase page size  

**Answer: B**  
Condition variables allow threads to sleep efficiently while waiting for a condition, avoiding CPU waste due to busy-waiting.

---
---

## Q1) Machine state (Ch. 4)

Which of the following is **NOT** part of the machine state of a process?  
A. Program counter  
B. CPU registers  
C. Page table / address space information  
D. Contents of the disk buffer cache  

**Answer: D**  
The machine state includes only what is required to resume a process (PC, registers, address space). The disk buffer cache is global OS state, not per-process.

---

## Q2) Process abstraction (Ch. 4)

The abstraction of a process mainly provides:  
A. A way to share memory between applications  
B. The illusion of a private CPU and private memory  
C. Faster disk access  
D. Automatic parallel execution  

**Answer: B**  
A process gives each program the illusion that it has exclusive control over the CPU and its own private memory.

---

## Q3) Context switch (Ch. 4)

During a context switch, the operating system must:  
A. Flush all CPU caches  
B. Save the current process’s execution state  
C. Terminate the running process  
D. Reload the program from disk  

**Answer: B**  
The OS saves registers, program counter, and related state so the process can resume execution later.

---

## Q4) Process states (Ch. 4)

Which transition is caused by an **I/O completion interrupt**?  
A. Running → Ready  
B. Ready → Running  
C. Blocked → Ready  
D. Running → Blocked  

**Answer: C**  
When I/O completes, a blocked process becomes ready to run again.

---

## Q5) fork() behavior (Ch. 5)

Immediately after `fork()`:  
A. Only the parent continues execution  
B. Only the child continues execution  
C. Both parent and child continue execution from the same point  
D. The parent blocks until the child exits  

**Answer: C**  
Both parent and child resume execution from the instruction following `fork()`.

---

## Q6) fork() and address space (Ch. 5)

After `fork()`, parent and child:  
A. Share the same stack  
B. Share the same heap  
C. Have separate virtual address spaces with identical contents  
D. Execute different programs  

**Answer: C**  
Each process has its own virtual address space, initially containing identical data (copy-on-write).

---

## Q7) exec() effect (Ch. 5)

Which resource is **replaced** by a successful `exec()`?  
A. PID  
B. Open file descriptors  
C. Address space  
D. Parent–child relationship  

**Answer: C**  
`exec()` replaces the entire address space while keeping the same PID.

---

## Q8) wait() semantics (Ch. 5)

If a process has no children and calls `wait()`:  
A. It blocks forever  
B. It immediately returns with an error  
C. It becomes a zombie  
D. It creates a child process  

**Answer: B**  
Calling `wait()` when no children exist causes an immediate error return.

---

## Q9) Zombie processes (Ch. 5)

Zombie processes exist to:  
A. Save CPU state for later execution  
B. Allow the parent to collect the child’s exit status  
C. Speed up future process creation  
D. Store swapped-out memory pages  

**Answer: B**  
The zombie retains exit information so the parent can retrieve it using `wait()`.

---

## Q10) Threads vs processes (Ch. 26)

Compared to processes, threads are:  
A. More isolated  
B. More expensive to create  
C. Lighter-weight execution units  
D. Always scheduled together  

**Answer: C**  
Threads are lighter-weight because they share address space and system resources.

---

## Q11) Thread machine state (Ch. 26)

Each thread has its own:  
A. Heap  
B. Address space  
C. Stack  
D. Global variables  

**Answer: C**  
Threads share address space but each thread has its own stack (stack" refers to a dedicated region of memory that stores the history of what a thread is currently doing). 

---

## Q12) Thread context switch (Ch. 26)

When switching between two threads of the same process:  
A. The address space must be changed  
B. Only registers and stack pointer need to be switched  
C. The process must be terminated  
D. The program must be reloaded  

**Answer: B**  
Threads of the same process share the address space, so only thread-specific state is switched.

---

## Q13) Race condition (Ch. 26)

A race condition can occur when:  
A. Two threads read the same variable  
B. Two threads write the same variable concurrently  
C. Threads execute on different CPUs only  
D. The scheduler is non-preemptive  

**Answer: B**  
A race condition requires shared data and at least one write, with outcome depending on execution order.

---

## Q14) Atomicity (Ch. 26)

An atomic operation is one that:  
A. Can be interrupted safely at any point  
B. Executes entirely or not at all  
C. Requires disabling interrupts always  
D. Is implemented only in software  

**Answer: B**  
Atomic operations appear indivisible to other threads.

---

## Q15) Critical section (Ch. 26)

The main goal of protecting a critical section is to:  
A. Improve cache locality  
B. Prevent deadlock  
C. Avoid race conditions  
D. Reduce context switch overhead  

**Answer: C**  
Critical sections are protected to prevent race conditions on shared data.

---

## Q16) Mutual exclusion (Ch. 26)

Mutual exclusion ensures that:  
A. Threads execute in parallel  
B. Only one thread accesses shared data at a time  
C. Threads never block  
D. Scheduling becomes deterministic  

**Answer: B**  
Mutual exclusion guarantees exclusive access to shared data.

---

## Q17) pthread_create (Ch. 27)

Creating a thread using `pthread_create()` requires specifying:  
A. A new address space  
B. A start routine (function)  
C. A new PID  
D. A page table  

**Answer: B**  
Threads execute a specified start routine within the same address space.

---

## Q18) Thread completion (Ch. 27)

If the main thread exits before other threads finish:  
A. Other threads continue running  
B. Only kernel threads are terminated  
C. The entire process terminates  
D. The threads become zombies  

**Answer: C**  
Exiting the main thread terminates the entire process and all its threads.

---

## Q19) pthread_join (Ch. 27)

`pthread_join()` is used to:  
A. Create a new thread  
B. Block until a thread finishes  
C. Put a thread to sleep  
D. Wake up waiting threads  

**Answer: B**  
`pthread_join()` blocks the caller until the specified thread terminates.

---

## Q20) Condition variables (Ch. 27)

Condition variables are mainly used to:  
A. Protect shared data  
B. Enforce mutual exclusion  
C. Coordinate waiting and signaling between threads  
D. Replace locks  

**Answer: C**  
Condition variables allow threads to sleep and be signaled when a condition becomes true.







# File 3 — Practice Exercises (Set 1)  
## CPU Scheduling (Ch. 7–10)

---

## Q1) Scheduling metrics (Ch. 7)

Which scheduling metric measures the time from when a job **arrives** until it **completes**?

A. Response time  
B. Waiting time  
C. Turnaround time  
D. Throughput  

**Correct answer: C — Turnaround time**

- **A. Response time — False**  
  Measures arrival → first time the job runs, not completion.
- **B. Waiting time — False**  
  Measures only time spent waiting in the ready queue.
- **C. Turnaround time — True**  
  Defined as completion time minus arrival time.
- **D. Throughput — False**  
  Measures how many jobs complete per unit time (system-wide).

---

## Q2) FIFO weakness (Ch. 7)

The main problem of FIFO scheduling when jobs have very different runtimes is known as:

A. Starvation  
B. Cache thrashing  
C. Convoy effect  
D. Priority inversion  

**Correct answer: C — Convoy effect**

- **A. Starvation — False**  
  FIFO eventually runs every job.
- **B. Cache thrashing — False**  
  Memory management issue, not scheduling.
- **C. Convoy effect — True**  
  A long job delays many short jobs behind it.
- **D. Priority inversion — False**  
  Occurs in priority-based scheduling, not FIFO.

---

## Q3) SJF vs STCF (Ch. 7)

Shortest Job First (SJF) differs from Shortest Time-to-Completion First (STCF) because:

A. STCF is non-preemptive  
B. SJF uses priorities  
C. STCF can preempt a running job  
D. STCF ignores job length  

**Correct answer: C — STCF can preempt a running job**

- **A. Non-preemptive — False**  
  STCF is preemptive by definition.
- **B. Uses priorities — False**  
  Neither SJF nor STCF uses priorities.
- **C. Can preempt — True**  
  STCF stops the running job if a shorter one arrives.
- **D. Ignores job length — False**  
  Job length is the core input.

---

## Q4) Round Robin trade-off (Ch. 7)

Which statement about Round Robin scheduling is correct?

A. It optimizes turnaround time  
B. It minimizes context switches  
C. It improves response time but can hurt turnaround time  
D. It requires knowing job lengths in advance  

**Correct answer: C — Improves response time but hurts turnaround time**

- **A. Optimizes turnaround — False**  
  RR often performs poorly on turnaround.
- **B. Minimizes context switches — False**  
  RR increases context switching.
- **C. Improves response time — True**  
  Each job gets CPU quickly.
- **D. Requires job lengths — False**  
  RR does not require job length knowledge.

---

## Q5) MLFQ intuition (Ch. 8)

MLFQ attempts to approximate SJF by:

A. Measuring job priority at compile time  
B. Assuming all jobs are CPU-bound  
C. Observing job behavior and adjusting priority dynamically  
D. Assigning fixed priorities  

**Correct answer: C — Observing behavior dynamically**

- **A. Compile-time priority — False**  
  MLFQ is runtime-based.
- **B. CPU-bound assumption — False**  
  Jobs may be interactive or I/O-bound.
- **C. Dynamic observation — True**  
  Priority changes based on observed CPU usage.
- **D. Fixed priorities — False**  
  Priorities change over time.

---

## Q6) MLFQ priority boost (Ch. 8)

The primary purpose of priority boosting in MLFQ is to:

A. Improve cache affinity  
B. Prevent starvation of CPU-bound jobs  
C. Reduce context switch cost  
D. Increase response time  

**Correct answer: B — Prevent starvation**

- **A. Cache affinity — False**  
  Not related to boosting.
- **B. Prevent starvation — True**  
  Long-running jobs are periodically boosted.
- **C. Reduce context switches — False**  
  Boosting may increase them.
- **D. Increase response time — False**  
  Boosting improves fairness, not response.

---

## Q7) Lottery scheduling fairness (Ch. 9)

In lottery scheduling, a process with 30 out of 100 tickets receives:

A. Exactly 30% of CPU every cycle  
B. Approximately 30% over long periods  
C. 30% only if CPU-bound  
D. Less than 30% due to randomness  

**Correct answer: B — Approximately over time**

- **A. Exactly every cycle — False**  
  Lottery is probabilistic.
- **B. Approximately over time — True**  
  Long-term statistical fairness.
- **C. Only if CPU-bound — False**  
  Ticket share applies regardless of behavior.
- **D. Less than share — False**  
  Expected share matches ticket proportion.

---

## Q8) Stride scheduling property (Ch. 9)

Compared to lottery scheduling, stride scheduling is:

A. Probabilistic and lightweight  
B. Randomized and faster  
C. Deterministic and exact in proportional share  
D. Unable to handle new processes  

**Correct answer: C — Deterministic and exact**

- **A. Probabilistic — False**  
  That describes lottery scheduling.
- **B. Randomized — False**  
  Stride uses deterministic arithmetic.
- **C. Deterministic and exact — True**  
  Guarantees precise proportional share.
- **D. Unable to handle new processes — False**  
  New processes can be added with proper initialization.

---

## Q9) Cache affinity (Ch. 10)

Cache affinity means:

A. Processes must always run on the same CPU  
B. Cache coherence guarantees performance  
C. Processes run faster when scheduled repeatedly on the same CPU  
D. Load balancing should never migrate processes  

**Correct answer: C — Faster on same CPU**

- **A. Must always — False**  
  Affinity is a preference, not a rule.
- **B. Coherence guarantees performance — False**  
  Coherence ensures correctness, not speed.
- **C. Faster on same CPU — True**  
  Cached data and TLBs remain warm.
- **D. Never migrate — False**  
  Migration may be required for load balance.

---

## Q10) SQMS vs MQMS (Ch. 10)

Which statement best compares single-queue and multi-queue multiprocessor schedulers?

A. Single-queue scales better  
B. Multi-queue avoids load imbalance completely  
C. Single-queue is simple but suffers from lock contention  
D. Multi-queue eliminates the need for migration  

**Correct answer: C — Lock contention in single-queue**

- **A. Scales better — False**  
  Single-queue does not scale well.
- **B. Avoids imbalance — False**  
  Multi-queue introduces load imbalance.
- **C. Simple but lock-heavy — True**  
  Global queue causes contention.
- **D. Eliminates migration — False**  
  Migration is often required in multi-queue systems.

---
# File 3 — Practice Exercises (Set 2)  
## CPU Scheduling (Ch. 7–10)

---

## Q11) Response time (Ch. 7)

Response time is defined as the time between:

A. Job arrival and job completion  
B. Job arrival and first time the job is scheduled  
C. Job creation and job termination  
D. Job completion and job exit  

**Correct answer: B — Arrival → first scheduled**

- **A. Arrival → completion — False**  
  This definition corresponds to turnaround time.
- **B. Arrival → first scheduled — True**  
  Response time measures how quickly a job first gets CPU time.
- **C. Creation → termination — False**  
  Not a standard scheduling metric.
- **D. Completion → exit — False**  
  Irrelevant for scheduling analysis.

---

## Q12) SJF limitation (Ch. 7)

The main practical limitation of Shortest Job First (SJF) scheduling is that:

A. It causes starvation of short jobs  
B. It requires knowing job length in advance  
C. It cannot be implemented preemptively  
D. It increases response time for interactive jobs  

**Correct answer: B — Requires knowing job length**

- **A. Starvation of short jobs — False**  
  Long jobs may starve, not short ones.
- **B. Requires knowing job length — True**  
  Accurate job-length prediction is rarely available in practice.
- **C. Cannot be preemptive — False**  
  STCF is the preemptive version of SJF.
- **D. Increases response time — False**  
  SJF generally improves turnaround, not response.

---

## Q13) Round Robin quantum size (Ch. 7)

If the time quantum in Round Robin scheduling is set too large, the scheduler behaves most similarly to:

A. Shortest Job First  
B. STCF  
C. FIFO  
D. Lottery scheduling  

**Correct answer: C — FIFO**

- **A. Shortest Job First — False**  
  SJF depends on job length knowledge.
- **B. STCF — False**  
  STCF is preemptive shortest-job scheduling.
- **C. FIFO — True**  
  With a very large quantum, jobs run until completion.
- **D. Lottery scheduling — False**  
  Lottery is proportional-share.

---

## Q14) MLFQ demotion rule (Ch. 8)

In a typical MLFQ scheduler, a job is demoted to a lower-priority queue when it:

A. Performs an I/O operation  
B. Voluntarily yields the CPU  
C. Uses its entire time slice  
D. Enters the ready queue  

**Correct answer: C — Uses its entire time slice**

- **A. Performs I/O — False**  
  Indicates interactive behavior and is often rewarded.
- **B. Voluntarily yields — False**  
  Suggests interactivity.
- **C. Uses entire time slice — True**  
  Indicates CPU-bound behavior.
- **D. Enters ready queue — False**  
  This is a neutral event.

---

## Q15) Starvation in MLFQ (Ch. 8)

Without priority boosting, MLFQ can suffer from:

A. Cache thrashing  
B. Starvation of CPU-bound jobs  
C. Priority inversion  
D. Deadlock  

**Correct answer: B — Starvation of CPU-bound jobs**

- **A. Cache thrashing — False**  
  Memory-management issue.
- **B. Starvation of CPU-bound jobs — True**  
  Interactive jobs may permanently dominate higher queues.
- **C. Priority inversion — False**  
  Lock-related problem.
- **D. Deadlock — False**  
  Scheduling alone does not cause deadlock.

---

## Q16) Lottery scheduling ticket transfer (Ch. 9)

Ticket transfer in lottery scheduling is useful to:

A. Reduce context switching  
B. Improve cache affinity  
C. Temporarily donate CPU share between cooperating processes  
D. Prevent starvation  

**Correct answer: C — Donate CPU share**

- **A. Reduce context switching — False**  
  Not related to ticket transfer.
- **B. Improve cache affinity — False**  
  Ticket transfer concerns fairness.
- **C. Donate CPU share — True**  
  Helps servers inherit client CPU share.
- **D. Prevent starvation — False**  
  Starvation is handled by proportional share itself.

---

## Q17) Stride scheduling data structure (Ch. 9)

Stride scheduling selects the next job to run based on:

A. The job with the smallest stride value  
B. The job with the smallest pass value  
C. The job with the largest number of tickets  
D. The job that arrived first  

**Correct answer: B — Smallest pass value**

- **A. Smallest stride — False**  
  Stride is fixed per job.
- **B. Smallest pass value — True**  
  Ensures deterministic proportional fairness.
- **C. Largest tickets — False**  
  Lottery scheduling concept.
- **D. Arrived first — False**  
  FIFO behavior.

---

## Q18) Cache coherence vs cache affinity (Ch. 10)

Which statement is correct?

A. Cache coherence guarantees high performance  
B. Cache affinity ensures correctness  
C. Cache coherence ensures correctness, cache affinity improves performance  
D. Cache affinity eliminates the need for migration  

**Correct answer: C — Correctness vs performance**

- **A. Guarantees performance — False**  
  Coherence ensures correctness only.
- **B. Ensures correctness — False**  
  Affinity is a performance optimization.
- **C. Correctness vs performance — True**  
  Coherence = correctness, affinity = speed.
- **D. Eliminates migration — False**  
  Migration may still be required.

---

## Q19) Multi-queue scheduling weakness (Ch. 10)

The primary weakness of multi-queue multiprocessor scheduling is:

A. Lock contention  
B. Poor scalability  
C. Load imbalance  
D. Starvation of short jobs  

**Correct answer: C — Load imbalance**

- **A. Lock contention — False**  
  Single-queue issue.
- **B. Poor scalability — False**  
  Multi-queue scales well.
- **C. Load imbalance — True**  
  Independent queues may unevenly distribute work.
- **D. Starvation of short jobs — False**  
  Not inherent to MQMS.

---

## Q20) Work stealing (Ch. 10)

Work stealing is best described as:

A. Periodically migrating all jobs to a single CPU  
B. Idle CPUs stealing jobs from busy CPUs  
C. Assigning jobs randomly at creation time  
D. Preventing cache misses by pinning jobs  

**Correct answer: B — Idle CPUs steal jobs**

- **A. Migrate all jobs — False**  
  Inefficient and unnecessary.
- **B. Idle CPUs steal jobs — True**  
  Dynamic load balancing mechanism.
- **C. Random assignment — False**  
  Static distribution.
- **D. Pinning jobs — False**  
  Opposite of stealing.

---

## Q21) Turnaround vs response time (Ch. 7)

Which workload benefits **more** from optimizing **response time** rather than turnaround time?

A. Batch processing jobs  
B. CPU-bound scientific simulations  
C. Interactive applications (e.g., shells, editors)  
D. Long-running background services  

**Correct answer: C — Interactive applications**

- **A. Batch processing jobs — False**  
  Batch workloads prioritize total completion time (turnaround).
- **B. CPU-bound simulations — False**  
  These are long-running and not user-interactive.
- **C. Interactive applications — True**  
  Users care about quick feedback, not total runtime.
- **D. Background services — False**  
  Throughput and stability matter more than response time.

---

## Q22) STCF starvation (Ch. 7)

Shortest Time-to-Completion First (STCF) can cause starvation because:

A. It never preempts running jobs  
B. Long jobs may never get CPU if short jobs keep arriving  
C. It gives all jobs equal priority  
D. It relies on time slices  

**Correct answer: B — Long jobs may starve**

- **A. Never preempts — False**  
  STCF is preemptive.
- **B. Long jobs may starve — True**  
  Short jobs can repeatedly preempt longer ones.
- **C. Equal priority — False**  
  Jobs are prioritized by remaining time.
- **D. Time slices — False**  
  Starvation comes from preemption policy, not time slicing.

---

## Q23) Round Robin quantum size (Ch. 7)

If the Round Robin time quantum is chosen **too small**, the system will mainly suffer from:

A. Starvation  
B. Convoy effect  
C. Excessive context switching  
D. Poor cache coherence  

**Correct answer: C — Excessive context switching**

- **A. Starvation — False**  
  Round Robin prevents starvation.
- **B. Convoy effect — False**  
  This is a FIFO issue.
- **C. Excessive context switching — True**  
  Very small quanta increase overhead and cache misses.
- **D. Poor cache coherence — False**  
  Coherence ensures correctness, not scheduling efficiency.

---

## Q24) MLFQ priority interpretation (Ch. 8)

In MLFQ, a job that frequently performs I/O is interpreted as:

A. CPU-bound  
B. Interactive  
C. Low priority  
D. Malicious  

**Correct answer: B — Interactive**

- **A. CPU-bound — False**  
  CPU-bound jobs use full time slices.
- **B. Interactive — True**  
  Frequent I/O indicates user interaction.
- **C. Low priority — False**  
  Interactive jobs remain high priority.
- **D. Malicious — False**  
  Scheduler does not judge intent.

---

## Q25) MLFQ gaming problem (Ch. 8)

A program that voluntarily yields the CPU just before its time slice expires is an example of:

A. Priority inversion  
B. Cache affinity optimization  
C. Gaming the scheduler  
D. Deadlock  

**Correct answer: C — Gaming the scheduler**

- **A. Priority inversion — False**  
  This involves locks and priorities.
- **B. Cache affinity optimization — False**  
  This behavior manipulates priority, not cache usage.
- **C. Gaming the scheduler — True**  
  The job pretends to be interactive to gain priority.
- **D. Deadlock — False**  
  No circular waiting occurs.

---

## Q26) Lottery scheduling randomness (Ch. 9) 

Lottery scheduling uses randomness primarily to:

A. Reduce scheduler overhead  
B. Guarantee deterministic execution order  
C. Approximate proportional-share fairness  
D. Prevent context switches  

**Correct answer: C — Approximate proportional-share fairness**

- **A. Reduce overhead — False**  
  Random choice does not reduce overhead.
- **B. Deterministic order — False**  
  Lottery scheduling is non-deterministic.
- **C. Approximate fairness — True**  
  Ticket ratios determine expected CPU share over time.
- **D. Prevent context switches — False**  
  Scheduling still involves context switching.
- Lottery scheduling is probabilistic and non-deterministic.You cannot tell predict with 100% which process will run next. 
---

## Q27) Stride scheduling precision (Ch. 9)

Stride scheduling is often described as the “deterministic version” of lottery scheduling because it:

A. Eliminates starvation completely  
B. Does not use tickets  
C. Produces exact proportional CPU allocation  
D. Avoids priority queues  

**Correct answer: C — Exact proportional allocation**

- **A. Eliminates starvation — False**  
  Starvation is not fully eliminated.
- **B. Does not use tickets — False**  
  Tickets are conceptually used to compute strides.
- **C. Exact proportional allocation — True**  
  Deterministic selection guarantees precise fairness.
- **D. Avoids priority queues — False**  
  Priority structures are typically required.

---

## Q28) Cache affinity harm (Ch. 10)

Which scheduling decision most directly **harms cache affinity**?

A. Keeping a job on the same CPU  
B. Migrating a job between CPUs frequently  
C. Using per-CPU run queues  
D. Using Round Robin locally  

**Correct answer: B — Frequent migration**

- **A. Same CPU — False**  
  Preserves cache locality.
- **B. Frequent migration — True**  
  Causes cache and TLB cold misses.
- **C. Per-CPU queues — False**  
  They improve affinity.
- **D. Local Round Robin — False**  
  Does not inherently break affinity.

---

## Q29) SQMS lock contention (Ch. 10)

In Single-Queue Multiprocessor Scheduling (SQMS), lock contention increases because:

A. Each CPU has its own run queue  
B. CPUs frequently migrate jobs  
C. All CPUs must synchronize on the same run queue  
D. Cache coherence is disabled  

**Correct answer: C — Shared run queue**

- **A. Per-CPU queues — False**  
  That describes MQMS.
- **B. Job migration — False**  
  Not the root cause of contention.
- **C. Shared run queue — True**  
  All CPUs compete for the same lock.
- **D. Cache coherence disabled — False**  
  Coherence is still present.
- Lock contention occurs when a thread attempts to acquire a lock that is already held by another thread. 
---

## Q30) MQMS load balancing (Ch. 10)

Why does Multi-Queue Multiprocessor Scheduling (MQMS) require **explicit load balancing**?

A. Jobs cannot be preempted  
B. CPUs do not share memory  
C. Each CPU schedules independently from its own queue  
D. Locks are never used  

**Correct answer: C — Independent scheduling**

- **A. No preemption — False**  
  Preemption is supported.
- **B. No shared memory — False**  
  CPUs do share memory.
- **C. Independent queues — True**  
  No global view of load leads to imbalance.
- **D. Locks never used — False**  
  Locks may still exist locally.

---

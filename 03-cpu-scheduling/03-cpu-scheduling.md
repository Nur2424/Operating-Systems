# Scheduling: Introduction (Chapter 7)

Up to now, we have focused on the **low-level mechanisms** that allow an operating system to run processes, such as context switching. Scheduling shifts the focus from *mechanisms* to **policies**.

**Scheduling answers the question:**
> Which process (or thread) should run next, and for how long?

While mechanisms define *how* the OS can switch between processes, scheduling policies define *which choice* the OS makes among competing runnable processes.

---

## Mechanism vs Policy (key distinction)

- **Mechanism**: how something is done  
  - context switch  
  - timer interrupt  
  - saving/restoring registers  

- **Policy**: which decision is made  
  - which process runs next  
  - how long it runs  

Scheduling is primarily about **policy**, not mechanism.

---

## Why scheduling is necessary

At almost all times, there are:
- many ready processes
- one (or a few) CPUs

The scheduler must decide how to share the CPU in a way that:
- improves efficiency
- provides fairness
- gives good responsiveness
- avoids starvation

---

## Workload assumptions

To analyze scheduling policies, we often make simplifying assumptions, such as:
- jobs arrive at known times
- CPU burst lengths are known
- no I/O (initially)
- context-switch cost is negligible

These assumptions are explicitly stated in exam problems and **must be respected** when solving them.

---

## Scheduling metrics (high importance)

Scheduling policies are evaluated using metrics such as:
- **Turnaround time**: completion time − arrival time
- **Waiting time**: time spent waiting in the ready queue
- **Response time**: time until the process first runs
- **Throughput**: number of jobs completed per unit time
- **Fairness**: whether all processes eventually make progress

Most numerical exam questions focus on **waiting time**, **turnaround time**, or **response time**.

---

## Scheduling disciplines

Scheduling policies are sometimes called **disciplines**.  
Different disciplines optimize different metrics and involve trade-offs.

There is no single “best” scheduler for all workloads.

---

## The crux of scheduling

The core questions addressed in this chapter are:
- How should scheduling policies be designed?
- What assumptions matter?
- Which metrics should be optimized?
- What basic scheduling approaches exist?

Understanding these questions is essential for analyzing and comparing schedulers in exams.

---
---

## 7.1 Workload Assumptions

Before analyzing scheduling policies, the operating system adopts a set of **simplifying assumptions** about the processes running in the system, collectively called the **workload**.

Scheduling policies are designed and evaluated based on these assumptions.  
The more we know about the workload, the more precisely we can design a scheduler.

The assumptions introduced here are **intentionally unrealistic**, but they are useful for building intuition.  
They will be gradually relaxed as more realistic scheduling policies are developed.

In this chapter, the terms **process** and **job** are used interchangeably.

---

### Workload Assumptions

1. **Each job runs for the same amount of time**  
   All jobs have identical CPU burst lengths.

2. **All jobs arrive at the same time**  
   There are no staggered arrivals or dynamic job creation.

3. **Once started, each job runs to completion**  
   Jobs are not preempted and do not yield the CPU voluntarily.

4. **All jobs only use the CPU**  
   Jobs perform no I/O and never block; only CPU execution is considered.

5. **The run-time of each job is known**  
   The scheduler knows exactly how long each job will execute.

---

### Why these assumptions matter

These assumptions simplify the analysis of scheduling policies and allow:
- clear comparison between different schedulers
- precise computation of waiting time, turnaround time, and response time
- theoretical understanding of scheduling behavior

Although unrealistic in real systems, these assumptions are essential for understanding the foundations of CPU scheduling.

---

### Exam reminder

- If a problem uses **SJF or STCF**, it implicitly assumes job run-times are known.
- If all jobs arrive at time 0 and use only the CPU, blocking states can be ignored.
- Always check which assumptions apply before solving scheduling problems.

---
---

## 7.3 First In, First Out (FIFO) — Explanation

### What is FIFO scheduling?
**FIFO (First In, First Out)**, also called **FCFS (First Come, First Served)**, is the simplest CPU scheduling policy.

- Processes (jobs) are executed **in the order they arrive**
- Once a job starts running, **it runs until completion**
- There is **no preemption** (the CPU is not taken away)

Think of FIFO like a **single checkout line**:
- Whoever gets in line first is served first
- Everyone else waits, even if their job is very short

---

### Why FIFO seems good at first
FIFO has some positive properties:

- Very **simple** to implement  
- Easy to understand  
- Works reasonably well **when all jobs are similar in length**

If all jobs take roughly the same amount of time, FIFO gives acceptable turnaround time.

---

### Turnaround time reminder
Turnaround time is defined as: Turnaround Time = Completion Time − Arrival Time

In early FIFO examples, all jobs arrive at time 0, therefore: Turnaround Time = Completion Time

---

### The main problem with FIFO: Convoy Effect
FIFO performs very poorly when:

- One **long job** arrives before many **short jobs**

This causes the **convoy effect**:

> Many short jobs get stuck waiting behind one long job.

#### Example intuition:
- Job A runs for 100 seconds
- Jobs B and C run for 10 seconds each
- Job A arrives first → B and C must wait 100 seconds

Result:
- Very high average turnaround time  
- Poor responsiveness  
- Unfair treatment of short jobs  

Because of this, FIFO is **not suitable for interactive systems**.

---

### Key exam takeaways
- FIFO is **non-preemptive**
- FIFO schedules jobs in **arrival order**
- FIFO is **simple but unfair**
- FIFO suffers from the **convoy effect**
- FIFO performs poorly when job lengths differ
- FIFO is not good for interactive systems

---

### One-line exam summary
FIFO scheduling executes jobs in arrival order without preemption; although simple, it suffers from the convoy effect and leads to poor average turnaround time when job lengths vary.

---
---

## 7.4 Shortest Job First (SJF)

### What problem is SJF trying to fix?

FIFO (FCFS) scheduling is simple but can perform very poorly when:
- A long job arrives before many short jobs
- Short jobs must wait a long time

This leads to a high **average turnaround time**, a phenomenon known as the **convoy effect**.

---

### What is Shortest Job First (SJF)?

**Shortest Job First (SJF)** is a scheduling policy that always selects the job with the **smallest CPU run-time** to execute next.

Under SJF:
- The shortest job runs first
- Then the next shortest job
- And so on until all jobs complete

---

### Why is SJF good?

- SJF **minimizes average turnaround time**
- Under the assumptions that:
  - All jobs arrive at the same time
  - The run-time of each job is known

Under these assumptions, **SJF is optimal**, meaning no other scheduling policy can achieve a lower average turnaround time.

---

### Example

Jobs:
- A: 100 seconds
- B: 10 seconds
- C: 10 seconds

#### FIFO order:
A → B → C  
Average turnaround time = **110 seconds**

#### SJF order:
B → C → A  

Turnaround times:
- B = 10
- C = 20
- A = 120  

Average turnaround time: (10 + 20 + 120) / 3 = 50 

SJF improves average turnaround time by more than a factor of two compared to FIFO.

---

### Key limitation of SJF

SJF relies on unrealistic assumptions:
- Job run-times are known in advance
- All jobs arrive at the same time

These assumptions rarely hold in real systems.

---

### Late arrival problem (convoy effect returns)

Example:
- Job A arrives at time t = 0 and runs for 100 seconds
- Jobs B and C arrive at time t = 10 and each run for 10 seconds

With **non-preemptive SJF**:
- Job A continues running
- Jobs B and C must wait until A completes

This again causes the **convoy effect**, and the average turnaround time becomes very high (≈ 103.33 seconds).

---

### Key takeaways (exam-oriented)

- **SJF = Shortest CPU burst first**
- **Optimal for average turnaround time** (under ideal assumptions)
- **Non-preemptive**
- Suffers when:
  - Job lengths are unknown
  - Jobs arrive at different times
- Motivates the need for **preemptive SJF**, also known as **Shortest Time-to-Completion First (STCF)**

---
---

## 7.5 Shortest Time-to-Completion First (STCF)

To address the limitations of Shortest Job First (SJF), we relax the assumption that jobs must run to completion. In particular, we allow the scheduler to **preempt** a running job.

SJF, by definition, is a **non-preemptive** scheduler: once a job begins executing, it runs until completion. This causes problems when long jobs arrive before short ones, leading to poor turnaround time for short jobs.

With timer interrupts and context switching, the scheduler can do better.

---

### Shortest Time-to-Completion First (STCF)

**STCF**, also known as **Preemptive Shortest Job First (PSJF)**, adds preemption to SJF.

At any point in time:
- The scheduler examines all jobs in the system
- It selects the job with the **least remaining execution time**
- If a new job arrives with a shorter remaining time than the currently running job, the scheduler **preempts** the running job

---

### Example behavior

- Job A arrives at time 0 and requires 100 seconds
- Jobs B and C arrive at time 10 and require 10 seconds each

Under STCF:
- A begins running at time 0
- At time 10, B and C arrive
- Since B and C have less remaining time than A, A is preempted
- B and C run to completion
- A resumes afterward

This significantly reduces the average turnaround time.

---

### Properties of STCF

- STCF is **preemptive**
- Scheduling decision is based on **remaining execution time**
- STCF avoids the convoy effect
- STCF requires timer interrupts and context switching
- Given known job runtimes, STCF is **provably optimal** for minimizing average turnaround time

---
---

## 7.6 A New Metric: Response Time

Up to this point, scheduling policies were mainly evaluated using **turnaround time**, which measures how long a job takes from arrival until completion. This metric works well for **batch systems**, where jobs run in the background and users do not interact directly with them.

However, turnaround time is not sufficient for **interactive systems**.

### Why Turnaround Time Is Not Enough

In interactive systems, users care about **how quickly they see a response**, not just when the job finishes.  
For example, if a user types a command and must wait many seconds before seeing any output, the system feels slow and unresponsive, even if the total execution time is short.

This limitation motivates the introduction of a new metric.

---

## Response Time

**Response time** measures how long a job waits **until it runs for the first time**.

Formally: T_response = T_first_run − T_arrival

### Key Difference from Turnaround Time

- **Turnaround time**: arrival → completion  
- **Response time**: arrival → first CPU execution

Response time captures **user-perceived performance**, which is critical in time-sharing and interactive systems.

---

## Why SJF and STCF Are Bad for Response Time

Schedulers such as **Shortest Job First (SJF)** and **Shortest Time-to-Completion First (STCF)** are excellent for minimizing turnaround time, but they can be very poor for response time.

If multiple jobs arrive at the same time, a job with longer execution time may not run at all until shorter jobs finish completely. As a result, its response time can be very large.

Thus:
- Good turnaround time
- Poor responsiveness

---

## Why Round Robin Is Better for Response Time

**Round Robin (RR)** scheduling divides CPU time into small time slices and cycles through all runnable jobs.

This ensures that:
- Every job runs quickly after arrival
- Response time is small
- No job waits too long for its first execution

The tradeoff is that turnaround time may increase slightly.

---

## Key Tradeoff (Exam Important)

It is usually **impossible to optimize both turnaround time and response time at the same time**.

- Batch systems → prioritize turnaround time → SJF / STCF  
- Interactive systems → prioritize response time → Round Robin  

---

## One-Line Exam Summary

Response time was introduced because turnaround time does not capture user-perceived performance in interactive systems.

---
---

## 7.7 Round Robin (RR)

### Motivation
Earlier scheduling algorithms such as **SJF** and **STCF** optimize **turnaround time**, but perform poorly for **interactive systems**, where users care about how quickly the system responds. This leads to the introduction of **Round Robin (RR)** scheduling, which focuses on **response time** and **fairness**.

---

### Core Idea
Round Robin scheduling runs each job for a fixed amount of time called a **time slice** (or **scheduling quantum**).  
When the time slice expires:
- The running job is **preempted**
- It is placed at the **end of the ready queue**
- The next job in the queue is scheduled

This process repeats until all jobs finish.

---

### Key Properties

**Preemptive Scheduler**
- Jobs can be interrupted using timer interrupts
- Context switching allows fair CPU sharing

**Fairness**
- Each job gets regular CPU access
- No job can monopolize the CPU

---

### Response Time vs Turnaround Time

**Response Time**
- Very good
- Jobs start running quickly after arrival

**Turnaround Time**
- Often poor
- Jobs finish later because execution is spread across time slices

---

### Time Slice Trade-off

Choosing the time slice length is critical:

| Time Slice Length | Effect |
|------------------|--------|
| Too small | Excellent response time, but high context-switch overhead |
| Too large | Behaves like FIFO, poor response time |
| Balanced | Good compromise between responsiveness and efficiency |

This trade-off is an example of **amortization**, where the cost of context switching is reduced by spreading it over longer execution intervals.

---

### Cost of Context Switching
Context switching overhead is not limited to saving and restoring registers. It also includes:
- Cache disruption
- TLB invalidation
- Branch predictor state loss

These costs can significantly impact performance if switching occurs too frequently.

---

### Comparison with Other Schedulers

| Scheduler | Optimizes | Performs Poorly |
|---------|-----------|-----------------|
| FIFO | Simplicity | Convoy effect |
| SJF / STCF | Turnaround time | Response time |
| Round Robin | Response time, fairness | Turnaround time |

---

### Exam-Oriented Key Points
- **Round Robin** is a **preemptive**, **time-sliced** scheduling algorithm
- Optimizes **response time**
- Ensures **fairness**
- Suffers from poor **turnaround time**
- Time slice selection is a critical design decision

---
---

## 7.8 Incorporating I/O

Up to this point, scheduling policies assumed that jobs only use the CPU.  
This assumption is unrealistic: real programs frequently perform **I/O operations** (disk, network, etc.).

---

### What happens during I/O?

When a running job issues an I/O request:
- It **cannot continue executing on the CPU**
- The job transitions from **RUNNING → BLOCKED**
- The CPU would be wasted if no other job runs

**Key rule (exam-critical):**
- **I/O request → BLOCKED**
- **I/O completion → interrupt → READY**

---

### Poor use of resources (Figure 7.8)

Example:
- Job **A**: alternates between short CPU bursts (10 ms) and I/O (10 ms)
- Job **B**: CPU-only for 50 ms

If the scheduler:
- Runs A
- Waits while A performs I/O
- Only runs B after A finishes completely

Then:
- CPU is idle during I/O
- Disk is idle during CPU execution
- Overall utilization is poor

This is an example of **wasted resources**.

---

### Overlap enables better utilization (Figure 7.9)

A better scheduler:
- Runs **B while A waits for I/O**
- When A’s I/O completes, preempts B and runs A
- Continues alternating in this way

This creates **overlap**:
- CPU executes one process
- Disk services another process
- System resources are used efficiently

**Key idea:**
> Overlap allows the CPU to execute one job while another waits for I/O.

---

### CPU bursts and scheduling

To incorporate I/O effectively:
- Each **CPU burst** is treated as a separate scheduling unit
- Instead of one long job, I/O-heavy jobs appear as many short CPU bursts

Schedulers such as **STCF** benefit from this:
- Short CPU bursts are favored
- Interactive (I/O-heavy) jobs run frequently
- CPU-intensive jobs fill idle gaps

---

### Why this matters

Incorporating I/O allows the scheduler to:
- Improve CPU utilization
- Improve responsiveness for interactive jobs
- Avoid idle hardware resources

---

### Exam summary

- I/O causes jobs to block
- Schedulers should run other ready jobs during I/O
- Overlapping CPU and I/O improves system efficiency
- Treating CPU bursts separately leads to better scheduling decisions

---
---

## 7.9 No More Oracle

Up to this point, many scheduling policies (such as **SJF** and **STCF**) relied on a very strong assumption:  
that the scheduler **knows the length of each job in advance**.

This assumption is often called an **oracle assumption**.

### Why this assumption is unrealistic

In a real, general-purpose operating system:

- The OS usually **does not know** how long a job will run
- Program execution depends on:
  - user input
  - loops with unknown bounds
  - I/O delays
  - network activity
- Even the program itself may not know its total run time

Thus, assuming perfect knowledge of job length is **the worst and least realistic assumption** made so far.

### Why SJF / STCF still matter

Even though the assumption is unrealistic:

- **SJF** and **STCF** are *provably optimal* when job lengths are known
- They provide a **theoretical ideal** for minimizing turnaround time
- Real schedulers aim to **approximate** their behavior without perfect knowledge

Think of SJF/STCF as:
> What we would like to do if we had perfect information.

### The key problem going forward

Now we are left with an important challenge:

- We want **low turnaround time** (like SJF/STCF)
- We want **good response time** (like Round Robin)
- We **do not know job lengths in advance**

This leads to two central questions:

1. How can we approximate SJF/STCF *without* knowing job length?
2. How can we incorporate ideas from Round Robin to maintain good response time?

These questions motivate the development of **adaptive schedulers**, such as:
- priority-based schedulers
- feedback-based schedulers
- multi-level feedback queues (MLFQ)

### Exam takeaways

- SJF / STCF assume known job lengths
- This assumption is unrealistic in real OSes
- Real schedulers do not have an oracle
- Practical schedulers must adapt dynamically and balance:
  - turnaround time
  - response time

---
---

## Chapter 7 — Scheduling: Introduction (Exam Summary)

This chapter introduces the **core ideas of CPU scheduling**, focusing on how an operating system decides **which job (process) runs next** and **why different policies exist**.

---

## Big Picture

Scheduling is about **trade-offs**.  
No single scheduling policy is best for all goals.

The chapter develops **two main families of schedulers**, each optimizing a different metric:

- **Turnaround-time–oriented schedulers**
- **Response-time–oriented schedulers**

Improving one usually **hurts the other**.

---

## Workload Assumptions (7.1)

To reason about schedulers, we start with simplified assumptions:

- All jobs arrive at the same time
- All jobs only use the CPU (no I/O)
- Each job runs to completion
- Job run times are known

These assumptions are **unrealistic**, but useful for understanding fundamentals.  
Later sections gradually relax them.

---

## Scheduling Metrics (7.2)

### Turnaround Time

Time from job arrival to job completion: T_turnaround = T_completion − T_arrival

- Primary metric for batch systems
- Optimized by SJF / STCF

### Response Time

Time from job arrival to its **first execution**: T_response = T_first_run − T_arrival

- Primary metric for interactive systems
- Optimized by Round Robin

### Key Trade-off
- Optimizing turnaround time often **hurts response time**
- Optimizing response time often **hurts turnaround time**

---

## FIFO / FCFS (7.3)

**First In, First Out (FIFO)** runs jobs in arrival order.

### Properties
- Very simple
- Non-preemptive
- Can cause the **convoy effect**

### Convoy Effect
Short jobs get stuck behind long jobs, leading to poor average turnaround time.

---

## Shortest Job First (SJF) (7.4)

**SJF runs the shortest job first**.

### Properties
- Non-preemptive
- Minimizes average turnaround time
- **Provably optimal** when all jobs arrive together and run times are known

### Limitation
- Suffers from the convoy effect when jobs arrive at different times

---

## Shortest Time-to-Completion First (STCF) (7.5)

**STCF = preemptive version of SJF**

### Key Idea
- Always run the job with the **least remaining time**
- Preempt the running job if a shorter one arrives

### Properties
- Preemptive
- Optimal for turnaround time (under known runtimes)
- Requires timer interrupts and context switching

---

## Response Time (7.6)

Batch schedulers (SJF/STCF) perform poorly for **interactive systems**.

- Users care about **how fast the system responds**
- Waiting a long time before first execution feels slow

This motivates schedulers that **quickly give every job CPU time**.

---

## Round Robin (RR) (7.7)

**Round Robin runs each job for a fixed time slice (quantum)**.

### Properties
- Preemptive
- Fair
- Excellent response time
- Time slicing improves interactivity

### Trade-off
- Shorter time slice → better response time
- Shorter time slice → more context switch overhead

RR is **good for response time**, but **bad for turnaround time**.

---

## Incorporating I/O (7.8)

When a job performs I/O:

- It becomes **blocked**
- Scheduler should run another job
- Overlapping CPU and I/O improves utilization

Schedulers treat **each CPU burst as a job**, allowing interactive jobs to run frequently while CPU-bound jobs run in the background.

---

## No More Oracle (7.9)

Up to now, schedulers assumed the OS knows job lengths.

This is the **worst assumption**:
- Real OSes do **not know the future**
- Job lengths are unpredictable

Thus:
- SJF / STCF are theoretical ideals
- Real schedulers must **approximate** them

---

## Final Takeaway (7.10 + Added Insight)

Two scheduler families emerged:

1. **SJF / STCF**
   - Optimize turnaround time
   - Poor response time
   - Require knowledge of job length

2. **Round Robin**
   - Optimize response time
   - Poor turnaround time
   - Fair and interactive

This trade-off is **fundamental**:
> You cannot optimize turnaround time and response time at the same time.

The OS also cannot see into the future.

### What’s Next
The next chapter introduces the **Multi-Level Feedback Queue (MLFQ)**:

- Uses **recent past behavior** to predict future behavior
- Approximates SJF/STCF without knowing job lengths
- Balances response time, turnaround time, and fairness

---

## Exam Keywords to Remember

- Turnaround time vs response time
- Convoy effect
- Preemptive vs non-preemptive
- Time slice / quantum
- Oracle assumption
- Trade-off between fairness and performance

---
---

# Chapter 8 — Scheduling: The Multi-Level Feedback Queue (MLFQ)

## Big Picture

So far, we have seen a fundamental tension in CPU scheduling:

- **SJF / STCF**
  - Excellent turnaround time
  - Requires knowing job length in advance (an oracle)
  - Poor response time for interactive workloads

- **Round Robin (RR)**
  - Good response time
  - Fair to all jobs
  - Poor turnaround time, especially for long jobs

In a real operating system, the scheduler **does not know job lengths ahead of time**, yet it must still provide:
- Good **turnaround time** for batch jobs
- Good **response time** for interactive jobs

This is the problem that **Multi-Level Feedback Queue (MLFQ)** is designed to solve.

---

## What Is MLFQ?

The **Multi-Level Feedback Queue (MLFQ)** is a scheduling approach that:

- Uses **multiple priority queues**
- Allows jobs to **move between queues**
- Adjusts priorities based on **observed behavior**
- Learns from the **recent past** to make better future decisions

Instead of relying on perfect knowledge of job length, MLFQ **infers behavior dynamically**.

---

## Core Goals of MLFQ

MLFQ explicitly targets two competing goals:

### 1. Minimize Turnaround Time
- Favor short jobs
- Avoid long jobs blocking short ones
- Similar to SJF/STCF, but without knowing job length

### 2. Minimize Response Time
- Ensure interactive jobs run quickly
- Avoid long waits before first execution
- Similar to Round Robin

MLFQ attempts to achieve **both goals simultaneously**.

---

## How MLFQ “Learns”

MLFQ does not ask:
> “How long will this job run?”

Instead, it observes:
> “How has this job behaved recently?”

Typical interpretations:
- Jobs that **use the CPU for long periods** → likely CPU-bound → lower priority
- Jobs that **frequently block for I/O** → likely interactive → higher priority

This adaptive behavior is why it is called **feedback**.

---

## Historical Context

- First described in **1962** in the CTSS system
- Influenced later systems such as **Multics**
- Earned Fernando Corbató the **Turing Award**
- Forms the conceptual basis for many modern schedulers

MLFQ is not just theoretical — it reflects **real OS design principles**.

---

## The Central Question (The Crux)

> **How can we schedule well without perfect knowledge of job length?**

More precisely:
- How can a scheduler minimize **response time** for interactive jobs
- While also minimizing **turnaround time** for batch jobs
- **Without a priori knowledge** of how long jobs will run?

MLFQ is the operating system’s answer to this question.

---

## Key Exam Sentence (Memorize This)

> **MLFQ approximates SJF/STCF using past behavior, while preserving good response time like Round Robin.**

If you can explain this sentence clearly, you understand the core idea of MLFQ.

---
---

# Chapter 8 – Scheduling: The Multi-Level Feedback Queue (MLFQ)
## Section 8.1: Basic Rules

---

## Why MLFQ?

From earlier scheduling policies:

- **SJF / STCF**
  - Optimizes **turnaround time**
  - Requires knowing job length in advance (unrealistic)

- **Round Robin**
  - Optimizes **response time**
  - Performs poorly for turnaround time

**MLFQ** is designed to:
- Achieve good **response time** for interactive jobs
- Achieve good **turnaround time** for CPU-bound jobs
- Work **without knowing job lengths ahead of time**

---

## Core Idea of MLFQ

MLFQ schedules jobs using:
- **Multiple queues**
- Each queue has a **different priority level**

Instead of assigning fixed priorities:
- MLFQ **changes job priority dynamically**
- Decisions are based on **observed behavior**

The scheduler:
- Learns from the *past behavior* of a job
- Uses that information to predict *future behavior*

---

## Structure of MLFQ

- There are multiple queues: `Q1, Q2, Q3, ...`
- Each queue represents a **priority level**
  - Higher queue → higher priority
- At any given time:
  - A job is in **exactly one queue**
- The scheduler:
  - Always selects a job from the **highest-priority non-empty queue**

### Scheduling within a queue
- If multiple jobs share the same priority:
  - They are scheduled using **Round Robin**

---

## Behavior-Based Priority Adjustment

MLFQ adjusts priorities based on how jobs behave while running.

### Interactive (I/O-bound) jobs
- Run briefly
- Frequently block for I/O (keyboard, disk, network)
- Interpretation:
  - Job needs fast response
- Result:
  - **Priority remains high**

### CPU-bound jobs
- Run for long periods without yielding
- Interpretation:
  - Job does not need immediate response
- Result:
  - **Priority is reduced over time**

This allows MLFQ to distinguish between:
- Interactive jobs
- CPU-intensive jobs

---

## The Two Fundamental MLFQ Rules

These rules are essential and should be memorized.

### Rule 1
**If Priority(A) > Priority(B), A runs (B does not).**

- Higher-priority jobs always preempt lower-priority jobs.

### Rule 2
**If Priority(A) = Priority(B), A and B run in Round Robin.**

- Jobs at the same priority level share the CPU fairly.

---

## Understanding the MLFQ Queue Example (Figure 8.1)

- Jobs **A** and **B**
  - Located in the highest-priority queue
  - Alternate using Round Robin
- Job **C**
  - Located in a middle-priority queue
- Job **D**
  - Located in the lowest-priority queue

As long as A or B are runnable:
- C and D **do not execute**

This highlights a key issue:
- **Starvation** of lower-priority jobs

(The book introduces this problem intentionally; later sections address it.)

---

## Key Exam Takeaways

- MLFQ uses **multiple priority queues**
- Job priority is **dynamic**
- Priority depends on **observed CPU vs I/O behavior**
- High priority → better response time
- Low priority → CPU-bound behavior
- Scheduling rules:
  - Higher priority always wins
  - Same priority → Round Robin

---
The maximum amount of CPU time the scheduler allows a job to run before it is preempted.
---

## 8.2 — Attempt #1: How to Change Priority (MLFQ)

### Big Picture

The goal of **MLFQ** is to behave like **SJF / STCF** *without knowing job lengths in advance*.

The operating system does **not** know:
- how long a job will run
- whether a job is interactive or CPU-bound

So MLFQ:
- **starts by guessing**
- then **adjusts priority based on observed behavior**

This section introduces the **first concrete rules** for changing priority over time.

---

## Core Idea

> Treat every new job as if it *might* be short and interactive.  
> Reward jobs that give up the CPU quickly.  
> Penalize jobs that consume long uninterrupted CPU bursts.

This is how MLFQ **learns from history**.

---

## Priority Adjustment Rules

### Rule 3 — Entry Rule

When a job enters the system:
- It is placed at the **highest priority queue**

**Reasoning:**
- Short or interactive jobs should run immediately
- This improves **response time**

---

### Rule 4a — CPU-Bound Penalty

If a job:
- **uses its entire time slice** while running

Then:
- Its priority is **reduced**
- The job moves **down one queue**

**Interpretation:**
- The job appears CPU-bound
- It does not require fast response
- It should be treated more like a batch job

---

### Rule 4b — I/O-Friendly Reward

If a job:
- **gives up the CPU before the it is expires** (time slice means the maximum amount of CPU time the scheduler allows a job to run before it is preempted.) 

- (e.g., blocks for I/O or user input)

Then:
- Its priority **stays the same**

**Interpretation:**
- The job appears interactive
- It should not be penalized
- Response time should remain low

---

## Example 1: A Single Long-Running Job

Behavior:
1. Job enters at highest priority
2. Uses full time slice → priority decreases
3. Repeats this behavior
4. Eventually reaches the lowest priority queue
5. Remains there

**Result:**
- CPU-bound jobs are gradually deprioritized
- Interactive jobs remain responsive

---

## Example 2: A Short Interactive Job Arrives Later

Scenario:
- Job A: long-running CPU-bound job
- Job B: short interactive job arrives later

What happens:
1. Job B enters at highest priority
2. Scheduler immediately runs B
3. Job B finishes quickly
4. Job A resumes afterward

**Result:**
- Short jobs finish quickly
- MLFQ approximates SJF behavior

---

## Example 3: I/O-Intensive Interactive Job

Job behavior:
- Uses CPU briefly
- Frequently blocks for I/O

MLFQ response:
- Job never uses a full time slice
- Priority never decreases
- Job stays in high-priority queues

**Result:**
- Interactive jobs remain responsive
- CPU-bound jobs do not dominate the CPU

---

## What MLFQ Achieves So Far

This first attempt:
- Approximates **Shortest Job First**
- Provides fast **response time**
- Prevents CPU-bound jobs from blocking others

---

## Problems With This Approach (Important for Exams)

Despite its strengths, this design has flaws:

1. **Starvation**
   - Too many interactive jobs can prevent long jobs from running

2. **Scheduler Gaming**
   - A job can yield just before the time slice ends
   - Remain at high priority indefinitely

3. **Phase Changes**
   - Jobs may shift between CPU-bound and interactive
   - Current rules do not adapt well

These issues motivate:
- **Priority Boost (Section 8.3)**
- **Better Accounting (Section 8.4)**

---

## Exam-Ready Summary Sentence

> MLFQ initially treats all jobs as short and interactive, then dynamically lowers priority for CPU-bound behavior and preserves priority for I/O-bound behavior in order to approximate SJF without knowing job lengths.

---
---

## 8.3 Attempt #2: The Priority Boost — Explanation

### The problem we are fixing: starvation

With the first MLFQ attempt (Rules 3, 4a, 4b), a serious problem appears:

- If there are many **interactive jobs**, they keep high priority.
- **CPU-bound (long-running) jobs** keep getting pushed down to lower queues.
- As a result, long-running jobs may **never get CPU time**.

This situation is called **starvation**.

---

### Core idea: Priority Boost

To solve starvation, MLFQ introduces a **priority boost**.

The idea is simple:

> Periodically reset the priorities of *all* jobs by moving them to the highest-priority queue.

This gives every job a fresh chance to run at high priority.

---

### Rule 5 (Priority Boost)

**Rule 5:**  
After some fixed time period **S**, move *all* jobs in the system to the topmost queue.

This rule applies regardless of:
- Current priority
- Past behavior
- Whether the job is CPU-bound or interactive

---

### What problems does the priority boost solve?

#### 1. Starvation
- CPU-bound jobs are guaranteed to eventually run.
- No job can remain forever at the lowest priority level.

#### 2. Changing behavior over time
- A job may change its nature:
  - CPU-bound → interactive
  - Interactive → CPU-bound
- After a boost, the scheduler can **re-evaluate** the job’s behavior.
- Jobs are not permanently penalized for past behavior.

---

### Intuition

MLFQ initially *assumes* every job might be interactive:
- If a job behaves interactively, it stays at high priority.
- If it behaves like a long-running job, it slowly moves down.

The priority boost acts like a **reset button**, ensuring fairness.

---

### The downside: choosing S

The boost interval **S** is difficult to tune:

- **S too large**
  - CPU-bound jobs may still starve for a long time
- **S too small**
  - Interactive jobs lose their advantage
  - Scheduling becomes less effective

Because of this, the book refers to **S** as a *“voodoo constant”*:
- It works
- But choosing the correct value is hard and system-dependent

---

### Exam-ready takeaway

- **Problem:** Starvation in MLFQ
- **Solution:** Periodic priority boost
- **Rule:** After time S, move all jobs to the top queue
- **Benefits:** Prevents starvation, adapts to behavior changes
- **Limitation:** Choosing S is non-trivial

---
---

## 8.4 Attempt #3: Better Accounting (MLFQ)

### The Problem: Gaming the Scheduler

In the previous version of MLFQ (Attempt #1), priority was adjusted based on **how** a job used the CPU:

- If a job used the **entire time slice** → its priority was lowered  
- If a job **gave up the CPU early** (e.g., due to I/O) → it kept the same priority  

While this helped interactive jobs, it introduced a serious flaw: **gaming the scheduler**.

A job could intentionally:
- Run for almost the entire time slice
- Issue an I/O operation just before the slice ended
- Appear “interactive” to the scheduler

As a result, priority would **not** be reduced, allowing the job to stay at a high-priority queue and unfairly consume CPU time, potentially starving other jobs.

---

### Key Insight: Count CPU Time, Not Behavior

The mistake was focusing on *how* the CPU was relinquished instead of *how much CPU time* was actually used.

The fix is **better accounting**:
- Track the **total CPU time used** by a job at each priority level
- Ignore whether the CPU was released early or late
- Base priority changes solely on accumulated CPU usage

---

### The Solution: Better Accounting in MLFQ

Each priority level now has a **CPU time allotment**.

- The scheduler keeps track of how much CPU time a job uses at its current level
- Once the job exhausts its allotted CPU time:
  - It is **demoted** to a lower-priority queue
- This happens **regardless of I/O behavior**

Whether the CPU time is used in:
- One long burst  
- Many short bursts  
- Bursts interrupted by I/O  

…the accounting is the same.

---

### Revised Rule (Critical for Exams)

Old rules 4a and 4b are replaced by a single rule:

**Rule 4:**  
Once a job uses up its time allotment at a given priority level, its priority is reduced (it moves down one queue), regardless of how many times it relinquished the CPU.

---

### Why This Works

Better accounting:
- Prevents scheduler gaming
- Preserves fairness
- Still favors interactive jobs
- Ensures CPU-bound jobs cannot dominate unfairly

This refinement makes MLFQ practical and robust enough for real operating systems.

---

### Exam Keywords to Remember

- Gaming the scheduler  
- CPU time allotment  
- Better accounting  
- Priority demotion  
- Interactive vs CPU-bound jobs  
- MLFQ Rule 4

---
---

## 8.5 Tuning MLFQ and Other Issues — Exam Notes

At this stage, MLFQ already works conceptually, but a major **practical problem** remains:

> How do we choose the right parameters?

There is **no single perfect configuration** for MLFQ.

---

### Core tuning questions (VERY EXAM-RELEVANT)

An MLFQ scheduler must decide:

1. How many priority queues should exist?
2. How long should the time slice be at each queue?
3. How often should priorities be boosted?

There is **no universal correct answer** — it depends on the workload.

**Exam tip:**  
If a question asks *why MLFQ is hard to design*, the answer is:
- Parameter tuning
- Dependence on workload behavior

---

### Different queues → different time slices

Most MLFQ implementations **do not use the same time slice for all queues**.

Typical design:

- **High-priority queues**
  - Short time slices (e.g., 5–10 ms)
  - Intended for interactive jobs
  - Improves response time

- **Low-priority queues**
  - Long time slices (e.g., 50–100 ms)
  - Intended for CPU-bound jobs
  - Reduces context-switch overhead

**Rule of thumb (exam-friendly):**
- Short jobs → short quanta  
- Long jobs → long quanta

---

### Why tuning is difficult: Ousterhout’s Law

This section introduces an important warning:

**Ousterhout’s Law (Voodoo Constants)**

Scheduler parameters often look like “magic numbers”  
(e.g., boost every 50 ms, 100 ms, or 1 second).

Problems:
- Boost interval too large → starvation
- Boost interval too small → too much overhead
- Default values often remain unchanged → not optimal

**Exam takeaway:**
- MLFQ is powerful but sensitive to configuration
- Poor tuning can ruin performance

---

### Real operating system implementations

Different systems implement MLFQ differently:

- **Solaris MLFQ**
  - Uses configuration tables
  - Tables define:
    - Priority levels
    - Time slice per level
    - Boost frequency
  - Administrator can tune values

- **FreeBSD scheduler**
  - Uses mathematical formulas
  - Priority depends on:
    - CPU usage
    - Usage decay over time
  - Avoids fixed tables

**Exam insight:**
> MLFQ is a scheduling concept, not a single fixed algorithm

---

### Additional features you may see in exam questions

- Some priority levels are reserved for kernel work
- User processes may never reach the highest priority
- Tools like `nice` allow users to influence (not guarantee) priority

---

### One-sentence exam summary

**MLFQ performance depends heavily on tuning (number of queues, time slices, and boost interval); while it adapts well to mixed workloads, poor parameter choices can lead to starvation, overhead, or unfairness.**

---
---

## 8.6 MLFQ — Summary (Book + Exam Notes)

### Book Summary (Condensed)

We have described a scheduling approach known as the **Multi-Level Feedback Queue (MLFQ)**.  
It is called *feedback* because it dynamically adjusts a job’s priority based on its **observed behavior over time**.

MLFQ maintains **multiple priority queues** and uses execution history as its guide:
- Jobs that behave like **short / interactive** jobs are favored
- Jobs that behave like **long / CPU-bound** jobs are gradually deprioritized

The refined set of MLFQ rules is:

- **Rule 1:** If `Priority(A) > Priority(B)`, A runs (B does not).
- **Rule 2:** If `Priority(A) = Priority(B)`, A and B run using Round Robin.
- **Rule 3:** When a job enters the system, it is placed at the **highest priority** queue.
- **Rule 4:** Once a job uses up its CPU allotment at a given level  
  (regardless of how many times it yielded the CPU), its priority is **reduced**  
  (it moves down one queue).
- **Rule 5:** After some time period **S**, **all jobs are boosted** to the topmost queue.

MLFQ is powerful because it **does not require a priori knowledge** of job length.  
Instead, it **observes execution behavior** and prioritizes jobs accordingly.

As a result, MLFQ:
- Approximates **SJF / STCF** for short, interactive jobs
- Preserves **fairness** and prevents starvation for long-running jobs

For these reasons, many real systems (BSD UNIX, Solaris, Windows NT and successors) use
a form of MLFQ as their **base scheduler**.

---

### Exam-Focused Summary (Added)

**What problem does MLFQ solve?**

MLFQ tries to solve **three competing goals at once**:

1. **Low turnaround time** (like SJF/STCF)
2. **Low response time** (like Round Robin)
3. **No starvation** (fairness over time)

But the OS **does not know job lengths**, so MLFQ:
- Starts by *assuming* jobs are short
- Learns behavior from CPU usage
- Adjusts priorities dynamically

---

### Key Ideas to Memorize for the Exam

- MLFQ is a **preemptive scheduler**
- Priority is **dynamic**, not fixed
- Short / interactive jobs:
  - Use little CPU
  - Stay at high priority
- Long / CPU-bound jobs:
  - Use full time slices
  - Gradually sink to lower queues
- **Priority boost** is essential to:
  - Prevent starvation
  - Handle phase changes in job behavior

---

### Common Exam Traps

- ❌ MLFQ does **not** know job length in advance  
- ❌ Yielding early does **not** guarantee high priority forever (Rule 4 fix)
- ❌ Without priority boost, starvation **can occur**
- ✅ MLFQ ≈ SJF + RR **without an oracle**

---

### One-Line Exam Definition

> **MLFQ is a scheduling algorithm that uses multiple priority queues and feedback from past CPU usage to approximate optimal scheduling without knowing job lengths in advance.**


---
---

# Scheduling: Proportional Share (Chapter 9)

## Big Idea

Previous scheduling policies focused on **performance metrics**, such as:
- **Turnaround time** (e.g., SJF, STCF)
- **Response time** (e.g., Round Robin, MLFQ)

**Proportional-share scheduling** takes a different approach.

Instead of asking:
- *Who should finish first?*
- *Who should respond fastest?*

It asks:
> **How much CPU time should each job receive?**

---

## What Is Proportional-Share Scheduling?

A **proportional-share (fair-share) scheduler** aims to guarantee that each job receives a **fixed fraction of the CPU over time**.

Example:
- Job A → 50% of CPU
- Job B → 30% of CPU
- Job C → 20% of CPU

The scheduler does **not** directly optimize turnaround time or response time.  
It ensures that, **over time**, CPU usage matches the specified proportions.

This is why it is often called a **fair-share scheduler**.

---

## How It Differs from Other Schedulers

| Scheduler | Primary Goal |
|---------|--------------|
| FIFO | Simplicity |
| SJF / STCF | Minimize turnaround time |
| Round Robin | Minimize response time |
| MLFQ | Balance turnaround & response |
| **Proportional Share** | **Fair CPU allocation** |

**Key exam point:**  
Proportional-share scheduling does *not* try to minimize turnaround or response time.

---

## Lottery Scheduling (Core Mechanism)

A well-known implementation of proportional-share scheduling is **lottery scheduling**.

### Basic Idea
- Each process is assigned a number of **tickets**
- Periodically, the scheduler:
  1. Draws a random ticket
  2. Schedules the process that owns that ticket

**More tickets ⇒ higher chance to run**

Example:
- Process A: 100 tickets
- Process B: 50 tickets

Expected CPU share:
- A ≈ 66%
- B ≈ 33%

Over time, CPU allocation converges to the ticket ratio.

---

## Why Randomness Is Acceptable

Lottery scheduling:
- Does **not** require knowing job lengths
- Does **not** predict the future
- Uses probability to approximate fairness

Short-term behavior may vary, but long-term CPU share is accurate.

This makes the approach:
- Simple
- Flexible
- Robust

---

## When Proportional-Share Scheduling Is Useful

Common use cases:
- Multi-user systems
- Virtual machines
- Containers
- Server environments with CPU guarantees

It answers questions like:
> *User X should receive twice as much CPU as User Y.*

---

## The Core Question (Crux)

> **How can we share the CPU proportionally without knowing job lengths in advance?**

This chapter explains:
- How tickets represent CPU share
- How proportional fairness is enforced
- Why deterministic scheduling is unnecessary
- Which mechanisms make proportional sharing effective

---

## Exam-Ready Summary

**Proportional-share scheduling guarantees each job a fraction of CPU time instead of optimizing turnaround or response time, often implemented using lottery scheduling with tickets.**

---
---

# Chapter 9 – Scheduling: Proportional Share

## Big Picture

Earlier schedulers focused on **time-based optimization**:
- **SJF / STCF** → minimize *turnaround time*
- **Round Robin** → minimize *response time*
- **MLFQ** → balance turnaround and response time *without knowing job length*

**Proportional-share scheduling changes the goal**.

Instead of optimizing *when* jobs finish, it guarantees **how much CPU time each job receives**.

---

## Proportional-Share Scheduling

A proportional-share (or fair-share) scheduler tries to ensure that:

> Each job receives a specific fraction of the CPU over time.

The scheduler does **not**:
- Predict job length
- Optimize turnaround time
- Optimize response time

It **only enforces proportional CPU allocation**.

---

## 9.1 Basic Concept: Tickets Represent Your Share

### Core Idea (Exam-Critical)

Each process is assigned a number of **tickets**.

> The fraction of tickets a process owns determines its share of the CPU.

If:
- Process A has 75 tickets
- Process B has 25 tickets
- Total tickets = 100

Then, over time:
- A receives ≈ 75% of CPU time
- B receives ≈ 25% of CPU time

This is the fundamental model.

---

## Lottery Scheduling

Proportional-share scheduling is commonly implemented using **lottery scheduling**.

### How it works

1. At each scheduling decision, the scheduler runs a lottery
2. It generates a random number in the range `[0, total_tickets - 1]`
3. The process that owns the selected ticket runs

Example:
- Tickets 0–74 → Process A
- Tickets 75–99 → Process B
- Random draw = 63 → A runs
- Random draw = 85 → B runs

---

## Probabilistic Fairness

Lottery scheduling is **not deterministic**.

- Short executions may appear unfair
- Over long periods, CPU usage converges to the correct proportions

Correct exam phrasing:
> Lottery scheduling provides **probabilistic fairness**, not deterministic fairness.

---

## Why Randomness Is Used

Randomized scheduling has important advantages:

### 1. Avoids pathological cases
- Deterministic schedulers can perform poorly on specific workloads
- Random selection avoids worst-case patterns

### 2. Lightweight
- No need for precise per-process CPU accounting
- Scheduler only tracks ticket counts

### 3. Fast
- Random number generation + lookup
- Very low scheduling overhead

---

## Important Distinctions (Exam Traps)

| Property | Lottery Scheduling |
|--------|-------------------|
Deterministic | No |
Short-term fairness | Not guaranteed |
Long-term fairness | Guaranteed |
Exact CPU slice every interval | No |
Proportional CPU share over time | Yes |

---

## Key Exam Takeaways

- Tickets represent **CPU share**, not priority
- More tickets → higher probability of running
- Scheduling decisions are randomized
- Fairness is **statistical**
- Long-running workloads converge to desired CPU percentages
- Proportional-share scheduling does **not** optimize turnaround or response time

---
---

## 9.2 Ticket Mechanisms — Explanation Notes

Lottery scheduling provides additional mechanisms that make proportional-share scheduling more flexible and practical. These mechanisms allow the scheduler to better model real-world situations such as user-level control, client/server interactions, and changing CPU demand.

---

### Ticket Currency

Ticket currency allows tickets to be managed **locally** while still preserving **global fairness**.

- The system maintains a **global ticket currency**.
- Users (or groups) may define their own **local currency** to divide their share among their own jobs.
- The scheduler automatically converts local ticket values into the global currency.

**Key idea**:  
Only the *relative share* matters, not the absolute number of tickets.

**Example intuition**:
- User A owns 50% of CPU time and runs two jobs.
- User B owns 50% of CPU time and runs one job.
- Even if User A assigns “500 tickets” to each job, the system normalizes these values so total CPU share remains fair.

**Why this is useful**:
- Users can manage their own CPU allocation without affecting others.
- The OS maintains overall proportional fairness.

---

### Ticket Transfer

Ticket transfer allows a process to **temporarily give its tickets to another process**.

This is especially useful in **client/server systems**.

**Problem**:
- A client with many tickets requests work from a server with few tickets.
- The server runs slowly, even though the client is waiting.

**Solution**:
- The client transfers its tickets to the server while the request is being handled.
- The server runs faster.
- After completing the work, tickets are returned to the client.

**Key idea**:  
CPU time should follow *who is doing the work*, not just who owns the tickets.

---

### Ticket Inflation

Ticket inflation allows a process to **temporarily increase or decrease** its number of tickets.

**Important constraints**:
- Only safe in **trusted environments**.
- Dangerous in competitive systems (a greedy process could monopolize the CPU).

**Use case**:
- A process knows it needs more CPU time temporarily.
- It increases its ticket count to reflect that need.
- The scheduler adjusts CPU share accordingly.

**Key idea**:  
Ticket inflation is a **hint**, not an enforced guarantee.

---

### Why Lottery Scheduling Uses Randomness

Lottery scheduling selects the next process to run by:
1. Picking a random number.
2. Selecting the process whose ticket range contains that number.

**Properties**:
- Fairness is achieved **probabilistically**, not deterministically.
- Short-term imbalance is possible.
- Long-term CPU shares converge to the desired proportions.

**Advantages of randomness**:
- Simple implementation
- Fast decision-making
- Minimal per-process accounting
- Avoids pathological worst-case behaviors

---

### Core Takeaways (Exam-Oriented)

- Tickets represent **CPU share**
- More tickets ⇒ higher probability of running
- Ticket currency enables **local control + global fairness**
- Ticket transfer lets **CPU follow work**
- Ticket inflation supports **cooperative adaptation**
- Lottery scheduling is **probabilistically fair**, not strictly fair

---
---

## 9.3 Implementation (Lottery Scheduling)

One of the most appealing aspects of lottery scheduling is the **simplicity of its implementation**.  
To make scheduling decisions, the system needs only:

- A **random number generator**
- A **data structure** holding runnable processes (e.g., a list)
- The **total number of tickets** across all runnable processes

No complex history tracking or priority recalculation is required.

---

### Basic Scheduling Procedure

Each runnable process owns a certain number of **tickets**.  
More tickets mean a higher probability of being scheduled.

To select the next process to run:

1. Compute the **total number of tickets**
2. Pick a **random number** in the range `[0, total_tickets - 1]`
3. Traverse the list of processes, **accumulating ticket counts**
4. The process whose cumulative ticket count exceeds the random number **wins the lottery**

---

### Example: Ticket Loop (Step-by-Step)

Assume three runnable processes:

- Process A → 100 tickets  
- Process B → 50 tickets  
- Process C → 250 tickets  

Total tickets = **400**

Ticket ranges:

- A: tickets `[0 .. 99]`
- B: tickets `[100 .. 149]`
- C: tickets `[150 .. 399]`

Now suppose the scheduler generates a random number: winner = 300

The scheduler traverses the process list:

- After A: cumulative = 100 → 100 < 300 → continue
- After B: cumulative = 150 → 150 < 300 → continue
- After C: cumulative = 400 → 400 > 300 → **C wins**

➡️ **Process C is scheduled to run**

---

### Example: Repeated Lottery Circulation

Suppose the scheduler performs multiple lotteries: Random picks: 63, 185, 70, 310, 12, 99, 240

Winning processes:

- 63  → A
- 185 → C
- 70  → A
- 310 → C
- 12  → A
- 99  → A
- 240 → C

Resulting schedule (one time slice each): A C A C A A C 

Although results vary in the short term, **over time** the fraction of CPU time converges to the ticket ratios.

---

### Key Property: Probabilistic Fairness

Lottery scheduling guarantees fairness **in expectation**, not deterministically.

- Short runs may appear unfair
- Long runs converge to proportional CPU sharing

This probabilistic behavior is acceptable (and often desirable) in many systems.

---

### Performance Notes

- The algorithm runs in **O(n)** time for `n` runnable processes
- Ordering processes by ticket count does **not affect correctness**
- Ordering can reduce traversal cost when a few processes hold most tickets

---

### Takeaway

Lottery scheduling provides:

- Proportional CPU sharing
- Simple implementation
- Low accounting overhead
- Robust fairness over time

All achieved using randomness and ticket counting. 

---
---

## 9.4 An Example (Lottery Scheduling Fairness)

To better understand the behavior of lottery scheduling, we examine a simple scenario with **two jobs** competing for the CPU.

### Experimental Setup

- Two jobs compete for the CPU
- Each job has:
  - The same number of tickets (100)
  - The same run time \( R \) (varied in the experiment)
- Scheduling is done using lottery scheduling

Ideally, both jobs should finish at roughly the same time.

---

### Randomness and Completion Time

Because lottery scheduling is **randomized**, one job may win more lotteries early and finish before the other, even though both jobs are identical.

To quantify this effect, we define an **unfairness metric**:

\[
U = \frac{\text{completion time of the first job}}{\text{completion time of the second job}}
\]

- If both jobs finish together, \( U \approx 1 \)
- If one job finishes much earlier, \( U \ll 1 \)
- A perfectly fair scheduler would achieve \( U = 1 \)

---

### Example

If:
- First job finishes at time 10
- Second job finishes at time 20

Then:

\[
U = \frac{10}{20} = 0.5
\]

This indicates unfairness.

---

### Observations from the Experiment

The experiment varies job length \( R \) from 1 to 1000 and averages results over multiple trials.

Key observations:
- When job length is **small**, unfairness is high
- As job length increases, unfairness decreases
- For long-running jobs, lottery scheduling approaches perfect fairness

---

### Key Insight

Lottery scheduling provides **probabilistic fairness**:

- Short-running jobs may experience unfairness
- Over many time slices, randomness averages out
- Long-running jobs receive CPU time proportional to their tickets

This behavior is expected and acceptable in randomized schedulers.

---
---

## 9.5 How To Assign Tickets?

One important problem not fully addressed by lottery scheduling is **how to assign tickets to jobs**.

The behavior of a proportional-share scheduler depends heavily on how tickets are allocated. Different ticket assignments can lead to very different system behavior.

One possible approach is to assume that **users know best**:
- Each user is given some number of tickets
- The user can distribute those tickets among their own jobs however they want

However, this approach is essentially a **non-solution**:
- It does not provide clear guidance on *how* tickets should be assigned
- It simply shifts the responsibility (and difficulty) to the user

As a result, given a set of jobs, the **ticket-assignment problem remains open**. There is no universally correct or automatic way to decide how many tickets each job should receive.

---
---

## 9.6 Why Not Deterministic? — Stride Scheduling

Lottery scheduling provides proportional fairness **probabilistically**, but not deterministically.  
Over short time scales, randomness can cause noticeable unfairness even if long-term fairness is achieved.

To address this, **stride scheduling** was introduced as a **deterministic fair-share scheduler**.

---

### Core Idea of Stride Scheduling

Each process has:
- **Tickets** → represent desired CPU share
- **Stride** → inversely proportional to tickets
- **Pass value** → tracks how much CPU the process has already received

A large constant (e.g., 10,000) is divided by the number of tickets to compute the stride.

Example:
- Process A: 100 tickets → stride = 100
- Process B: 50 tickets → stride = 200
- Process C: 250 tickets → stride = 40

The scheduler always runs the process with the **lowest pass value**.

After running for one time slice: pass = pass + stride

---

### Stride Scheduling Algorithm (Conceptual)

1. Pick the process with the smallest pass value
2. Run it for one time slice
3. Increment its pass value by its stride
4. Reinsert it into the queue
5. Repeat

This guarantees **exact proportional CPU allocation**.

---

### Figure 9.3 — Stride Scheduling: A Trace (Markdown Table)

| Pass(A) (stride=100) | Pass(B) (stride=200) | Pass(C) (stride=40) | Who Runs |
|----------------------|----------------------|---------------------|----------|
| 0                    | 0                    | 0                   | A        |
| 100                  | 0                    | 0                   | B        |
| 100                  | 200                  | 0                   | C        |
| 100                  | 200                  | 40                  | C        |
| 100                  | 200                  | 80                  | C        |
| 100                  | 200                  | 120                 | A        |
| 200                  | 200                  | 120                 | C        |
| 200                  | 200                  | 160                 | C        |
| 200                  | 200                  | 200                 | ...      |

From this trace:
- C runs **5 times**
- A runs **2 times**
- B runs **1 time**

This exactly matches their ticket ratios:
- C: 250 tickets
- A: 100 tickets
- B: 50 tickets

---

### Lottery vs Stride Scheduling (Exam Insight)

- **Lottery Scheduling**
  - Probabilistic fairness
  - Simple
  - Easy to add new processes
  - No per-process global state

- **Stride Scheduling**
  - Deterministic fairness
  - Exact proportional allocation
  - Requires maintaining pass values
  - Harder when new processes arrive dynamically

**Key takeaway:**  
Lottery scheduling trades precision for simplicity, while stride scheduling trades simplicity for exact fairness.

---
---

# Chapter 9 — Scheduling: Proportional Share

## Core Idea: Fair Sharing Instead of Speed

Earlier scheduling policies focused on **performance metrics**:

- **SJF / STCF** → minimize **turnaround time**
- **Round Robin** → minimize **response time**
- **MLFQ** → balance both without knowing job length

In contrast, **proportional-share scheduling** asks a different question:

> How much of the CPU should each job receive?

The goal is to **guarantee a percentage of CPU time** to each job, rather than deciding which job finishes first.

This is why proportional-share scheduling is also called **fair-share scheduling**.

---

## Lottery Scheduling

### Basic Idea

Lottery scheduling implements proportional sharing using **randomness**.

- Each process owns some number of **tickets**
- The fraction of tickets a process owns equals its **share of the CPU**
- Every time slice, the scheduler:
  1. Draws a random ticket
  2. Runs the process that owns that ticket

### Example

- Process A: 75 tickets  
- Process B: 25 tickets  
- Total: 100 tickets  

Expected CPU usage:
- A → 75%
- B → 25%

Over short time scales, results may be unfair.  
Over long time scales, CPU usage converges to the desired proportions.

### Properties

- ✔ Simple
- ✔ Flexible
- ✖ Only **probabilistically fair** (not exact at every moment)

---

## Why Randomness Helps

Randomness in scheduling:

- Avoids worst-case pathological behaviors
- Requires little per-process state
- Is fast to compute
- Works well when exact fairness is not immediately required

However, randomness **cannot guarantee exact proportions at every instant**.

---

## Ticket Mechanisms

Lottery scheduling supports several powerful extensions.

### 1. Ticket Currency

Allows users to subdivide their tickets among their own jobs.

Example:
- User A has 100 tickets
- Runs two jobs → assigns 50 tickets to each
- System automatically converts these into global tickets

This enables **hierarchical resource sharing**.

---

### 2. Ticket Transfer

Useful in **client/server systems**.

- A client temporarily gives its tickets to a server
- The server runs faster on behalf of the client
- Tickets are returned when the request completes

This avoids priority-inversion–like problems.

---

### 3. Ticket Inflation

A process can temporarily increase or decrease its ticket count.

- ⚠ Dangerous if processes do not trust each other
- ✔ Useful in cooperative environments

---

## Implementation Simplicity

Lottery scheduling is easy to implement:

1. Generate a random number in `[0, total_tickets)`
2. Traverse the process list while summing tickets
3. The first process whose cumulative sum exceeds the random number runs

There is:
- No job-length prediction
- No complex priority queues
- Minimal per-process state

---

## Fairness Analysis

The chapter defines an **unfairness metric**: U = finish_time_of_first_job / finish_time_of_second_job

- Perfect fairness → `U ≈ 1`
- Short jobs → more unfairness
- Long-running jobs → fairness improves over time

Key insight:

> Lottery scheduling becomes fair **only over long time scales**.

---

## Why Not Deterministic? → Stride Scheduling

To eliminate randomness, **stride scheduling** was introduced.

### Stride Scheduling Concepts

Each process has:
- **Tickets**
- **Stride** = large_constant / tickets
- **Pass value**

Scheduling rule:
- Run the process with the **lowest pass value**
- After running, increment its pass by its stride

### Properties

- ✔ Deterministic
- ✔ Exactly proportional sharing
- ✖ Requires global per-process state
- ✖ More complex to manage dynamically

---

## Why Lottery Scheduling Still Matters

Compared to stride scheduling, lottery scheduling:

- Has no per-process global state
- Handles new processes naturally
- Is easier to integrate dynamically

---

## Final Takeaways (Exam Important)

- **Proportional-share scheduling** focuses on fair CPU distribution
- **Lottery scheduling** achieves fairness probabilistically using tickets
- **Stride scheduling** achieves fairness deterministically using strides
- These schedulers are best when:
  - CPU shares are easy to define
  - Jobs are long-running
  - Fairness matters more than response or turnaround time

General-purpose OSes usually prefer **MLFQ**,  
while **virtualized or controlled environments** often prefer proportional-share schedulers.

---
---

# Chapter 10 — Multiprocessor Scheduling (Advanced)

## Introduction

This chapter introduces the basics of **multiprocessor scheduling**. Because this topic is relatively advanced, it is best studied after covering **concurrency** in some detail. Historically, multiprocessor systems appeared first in high-end computing, but today they are commonplace in desktops, laptops, and mobile devices due to the rise of **multicore processors**.

A multicore processor places multiple CPU cores on a single chip. This shift happened because increasing the speed of a single CPU became difficult without excessive power consumption. As a result, modern systems provide multiple CPUs/cores, which enables higher potential performance—**if software can exploit parallelism**.

However, adding more CPUs introduces new challenges. A typical single-threaded application only uses one CPU; simply adding CPUs does not make such applications faster. To benefit from multiple CPUs, applications must be rewritten to run **in parallel**, typically using **threads**. Multithreaded applications can distribute work across CPUs and achieve better performance when more CPU resources are available.

Beyond application design, the operating system faces new problems when scheduling across multiple CPUs. Many principles from single-CPU scheduling still apply, but **new issues arise** that require different approaches.

---

## The Crux: How to Schedule Jobs on Multiple CPUs

**Key questions addressed in this chapter:**
- How should the OS schedule jobs on multiple CPUs?
- What new problems arise in multiprocessor systems?
- Do single-CPU scheduling techniques still work, or are new ideas required?

---

## Why Multiprocessor Scheduling Is Hard (High-Level)

- **Parallelism is not automatic:** single-threaded programs do not benefit from multiple CPUs.
- **Caches matter:** each CPU has its own cache; moving a thread between CPUs can hurt performance.
- **Synchronization costs increase:** locks and shared data can limit scalability.
- **Scheduling trade-offs emerge:** balancing CPU utilization, cache locality, and fairness is harder than in single-CPU systems.

---

> **Key takeaway:** Multiprocessor scheduling introduces fundamental trade-offs between **load balancing**, **cache affinity**, and **synchronization overhead**. No scheduler can optimize all of them simultaneously.

---
---

# 10.1 Background: Multiprocessor Architecture

## Goal of this section
This section explains **why multiprocessor scheduling is fundamentally harder than single-CPU scheduling**.  
The core reason is the presence of **CPU caches** and how they interact when **multiple CPUs share the same main memory**.

Before studying scheduling policies, we must understand the **hardware differences** between:
- Single-CPU systems
- Multi-CPU (multiprocessor / multicore) systems

---

## Single-CPU case (simpler case)

In a system with **one CPU**:

- The CPU has a **cache** (small, fast memory)
- Main memory is **large but slow**
- When data is accessed:
  - First access → fetched from main memory (slow)
  - Data is copied into the CPU cache
  - Subsequent accesses → fetched from cache (fast)

### Why caches work well
Caches rely on **locality**:
- **Temporal locality**: data accessed once is likely accessed again soon
- **Spatial locality**: data near a recently accessed address is likely accessed next

Because there is only **one CPU**, cache behavior is predictable and safe.

---

## What changes with multiple CPUs?

In a **multiprocessor system**:

- Each CPU has **its own private cache**
- All CPUs share the **same main memory**

This creates a new and serious problem.

---

## Cache coherence problem (key concept)

Consider this scenario:

1. A program runs on **CPU 1**
2. CPU 1:
   - Reads value `D` from memory address `A`
   - Stores `D` in its cache
   - Modifies it to `D′`
   - Writes only to its cache (write-back cache)
3. The OS **moves the process to CPU 2**
4. CPU 2:
   - Tries to read address `A`
   - Its cache does not contain the updated value
   - Fetches the old value `D` from memory

Result:
- CPU 2 sees **stale data**
- Program behavior becomes incorrect

This is known as the **cache coherence problem**.

---

## Cache coherence

**Cache coherence** means:

> All CPUs must observe a **consistent view of shared memory**, even though each CPU has its own cache.

Without cache coherence:
- CPUs may see different values for the same memory location
- Programs may behave incorrectly
- Scheduling decisions become dangerous

---

## How cache coherence is handled

Cache coherence is mainly solved by **hardware**, not the OS.

### Common technique: Bus snooping
- Each cache monitors (“snoops”) memory bus traffic
- When one CPU updates a memory location:
  - Other CPUs detect the change
  - They either:
    - **Invalidate** their cached copy, or
    - **Update** it with the new value

This ensures all CPUs maintain a consistent view of memory.

---

## Why this matters for scheduling

When the OS schedules processes on multiple CPUs:
- Moving a process between CPUs causes **cache loss**
- Cached data must be reloaded
- Performance decreases

Thus, multiprocessor scheduling must consider:
- **Which CPU a process runs on**
- **How often it migrates**
- **Cache reuse and affinity**

---

## Key exam takeaway

> Multiprocessor scheduling is harder than single-CPU scheduling because each CPU has its own cache, and moving processes between CPUs can cause cache coherence problems and performance loss.

---
---

## 10.2 Don’t Forget Synchronization

Even though modern multiprocessor hardware provides **cache coherence**, this does **not** mean that programs (or the OS) can safely access shared data without synchronization.

### Cache Coherence vs. Correctness

Cache coherence guarantees that:
- Updates to shared memory eventually become visible to all CPUs.

However, cache coherence **does NOT guarantee correctness**:
- It does not make operations atomic.
- It does not prevent race conditions.
- It does not ensure consistent updates to shared data structures.

Thus:

> **Cache coherence ≠ synchronization**

---

### The Core Problem

When multiple CPUs (or threads) **concurrently access and update shared data**, race conditions can occur even if caches are coherent.

For example, consider a shared linked list accessed by multiple CPUs. If two threads run the same removal code at the same time:
- Both may read the same `head` pointer.
- Both may update `head`.
- Both may free the same node.

This can lead to:
- Double frees
- Corrupted data structures
- Incorrect results

Each thread has its own:
- Registers
- Stack
- Local variables

So interleavings can occur that break correctness.

---

### Why Locks Are Still Required

To ensure correctness when accessing shared data:
- **Mutual exclusion** primitives (e.g., locks, mutexes) must be used.
- Locks ensure that only one CPU/thread enters a critical section at a time.

Even with cache coherence:
- Updating shared data structures **must be synchronized**.

---

### Key Takeaways

- Cache coherence ensures **visibility**, not **atomicity**.
- Synchronization ensures **correctness**.
- Shared data structures accessed across CPUs **require locks**.
- Without synchronization, race conditions occur even on coherent hardware.

---

### Exam-Friendly Summary

- Cache coherence handles *where data comes from*.
- Synchronization handles *who is allowed to update data*.
- Correct multiprocessor programs require **both**.


---
---

## 10.3 One Final Issue: Cache Affinity

### What problem does cache affinity address?

In a **multiprocessor system**, a process or thread can be scheduled on **different CPUs over time**.  
The key question for the scheduler is:

> Should a process be freely moved between CPUs, or should it stay on the same CPU when possible?

This question leads to the concept of **cache affinity**.

---

### What is cache affinity?

**Cache affinity** refers to the idea that a process runs **faster** when it continues to execute on the **same CPU**.

Why?

- While running, a process fills the CPU’s **cache** and **TLB** with useful data
- Instructions, stack data, and frequently accessed memory are cached
- If the process runs again on the same CPU, it can reuse this cached state

This results in:
- Fewer cache misses
- Less memory traffic
- Better performance

---

### What happens if the process moves to another CPU?

If the scheduler runs the process on a **different CPU**:

- That CPU’s cache does **not** contain the process’s data
- The process must reload its working set from main memory
- This causes cache misses and slows execution

Important clarification:
- The process still runs **correctly**
- Hardware cache coherence protocols guarantee correctness
- However, performance is worse

So:

- **Cache coherence** → correctness  
- **Cache affinity** → performance

---

### Why should the scheduler care?

A multiprocessor scheduler must balance two competing goals:

- **Load balancing**: evenly distribute work across CPUs
- **Cache affinity**: keep processes on the same CPU to improve performance

A good scheduler:
- Prefers to keep a process on the same CPU
- Moves it only when necessary (e.g., CPU overload or imbalance)

---

### One-line exam definition

> **Cache affinity** is the tendency of a process to perform better when scheduled repeatedly on the same CPU because its working set remains in that CPU’s cache.

---

### Common exam traps

- Cache affinity is **not required for correctness**
- Cache coherence **does not eliminate** cache affinity issues
- Processes **may be moved**, but doing so can hurt performance

---

### Key takeaway

- **Cache coherence ensures correctness**
- **Cache affinity improves performance**
- Multiprocessor schedulers should consider both

---
---

## 10.4 Single-Queue Scheduling (SQMS)

### Core Idea
Single-Queue Multiprocessor Scheduling (SQMS) is the most basic way to schedule jobs on a multiprocessor system.  
All runnable jobs are placed into **one global run queue**, and **all CPUs pull jobs from this same queue**.

> Think of it as extending single-CPU scheduling to multiple CPUs by simply letting more CPUs pick jobs from the same list.

---

### Why SQMS Is Attractive

- **Simplicity**  
  Existing single-CPU scheduling policies (FIFO, SJF, Round Robin, etc.) can be reused.
- **Minimal design effort**  
  No need to invent a new scheduling algorithm.
- **Correctness is easy**  
  One queue gives a clear global ordering of jobs.

---

### Problem #1: Scalability

Because all CPUs share the same queue:
- The queue must be protected by a **lock**
- Only **one CPU at a time** can access it

As the number of CPUs increases:
- Lock contention increases
- CPUs spend more time waiting for the lock
- Less time is spent doing useful work

**Key takeaway:**  
> SQMS does **not scale well** as CPU count grows.

---

### Problem #2: Cache Affinity

**Cache affinity** means a process runs faster if it keeps running on the same CPU, because its data remains in that CPU’s cache.

In SQMS:
- A job may run on CPU 0, then CPU 2, then CPU 1
- Its cache state is repeatedly lost
- The job must reload data from memory

This leads to:
- More cache misses
- Worse performance
- Even though execution is still correct

---

### Example: Job Migration Problem

With jobs A–E and 4 CPUs:
- Each CPU repeatedly pulls the “next” job
- Jobs bounce across CPUs over time
- Cache locality is destroyed

Schedulers may try to add **affinity heuristics** to keep jobs on the same CPU when possible, but this:
- Adds complexity
- Does not fully fix scalability issues

---

### Strengths of SQMS
- Very simple to implement
- Easy to adapt from single-CPU schedulers

### Weaknesses of SQMS
- Poor scalability due to lock contention
- Poor cache affinity due to frequent job migration

---

### Exam-Ready Summary
> Single-Queue Multiprocessor Scheduling reuses single-CPU scheduling logic by placing all jobs in one global queue.  
> While simple, it scales poorly because of lock contention and performs badly with respect to cache affinity.

---
---

## 10.5 Multi-Queue Multiprocessor Scheduling (MQMS)

### Motivation
Single-Queue Multiprocessor Scheduling (SQMS) suffers from:
- Poor scalability due to lock contention
- Weak cache affinity
- Global queue bottlenecks

To address these issues, systems adopt **Multi-Queue Multiprocessor Scheduling (MQMS)**.

---

## Core Idea

- Each CPU has **its own scheduling queue**
- Jobs are placed into **one queue only**
- Each CPU schedules **independently**
- Each queue can use a standard policy (e.g., Round Robin)

This avoids global locks and improves cache locality.

---

## Example: Two CPUs, Four Jobs

Assume:
- CPUs: CPU 0 and CPU 1
- Jobs: A, B, C, D

### Queue Assignment 

Q0 → A → C
Q1 → B → D

Each CPU schedules from its own queue.

---

## Example Schedule (Round Robin per Queue)

### Timeline

CPU 0: A | A | C | C | A | A | C | C | …
CPU 1: B | B | D | D | B | B | D | D | …

Each CPU alternates between its local jobs.

---

## Advantages of MQMS

### 1. Scalability
- No global run queue
- No global lock
- CPU count can increase without contention explosion

### 2. Cache Affinity
- Jobs tend to stay on the same CPU
- Cache contents are reused
- Fewer cache misses and reloads

---

## Fundamental Problem: Load Imbalance

Because queues are independent, **load may become uneven**.

### Example: Job Completion Causes Imbalance

Suppose job C finishes: 

Q0 → A
Q1 → B → D

Now scheduling proceeds as:

CPU 0: A | A | A | A | A | …
CPU 1: B | B | D | D | B | …

### Result:
- CPU 0 runs only A
- CPU 1 splits time between B and D
- **A gets twice as much CPU**
- Fairness is broken

---

## Worse Case: Idle CPU

If both A and C finish:

Q0 → (empty)
Q1 → B → D

### Resulting Timeline

CPU 0: IDLE | IDLE | IDLE | …
CPU 1: B | B | D | D | …

🚨 **CPU 0 is completely idle while work exists on CPU 1**

---

## The Crux: Load Imbalance

> MQMS solves scalability and cache affinity  
> but **does not automatically balance load**

---

## Solution: Migration

### Job Migration
- Move jobs from overloaded CPUs to idle ones
- Restores balance
- Improves utilization

### Simple Migration Example

Before:
Q0 → (empty)
Q1 → B → D

After migration:
Q0 → B
Q1 → D

---

## Harder Case: Continuous Imbalance

Q0 → A
Q1 → B → D

A single migration doesn’t solve the problem permanently.

### Possible Timeline with Migration

CPU 0: A | A | B | A | B | …
CPU 1: B | D | D | D | A | …

Jobs are periodically moved to rebalance load.

---

## Work Stealing (Important)

A common modern technique is **work stealing**:

- Idle CPU checks another CPU’s queue
- If another queue is full, it **steals a job**
- Load is balanced dynamically

### Trade-offs

| Too Frequent Stealing | Too Rare Stealing |
|----------------------|------------------|
| High overhead        | Load imbalance  |
| Poor scalability     | Idle CPUs       |

Finding the right balance is a **policy challenge**.

---

## Summary of MQMS

| Aspect | MQMS |
|------|------|
| Scalability | Excellent |
| Cache Affinity | Strong |
| Lock Contention | Minimal |
| Load Balance | Manual / Migration |
| Complexity | Higher than SQMS |

---

## One-Line Exam Takeaway 🚨

**Multi-queue scheduling improves scalability and cache affinity by using per-CPU queues, but introduces load imbalance that must be addressed via migration techniques such as work stealing.**

---
---

## 10.6 Linux Multiprocessor Schedulers

In the Linux community, there has never been a single universally accepted solution for building a multiprocessor scheduler. Over time, **three different schedulers** have been developed, each reflecting a different design philosophy:

- **O(1) Scheduler**
- **Completely Fair Scheduler (CFS)**
- **Brain Fuck Scheduler (BFS)**

This diversity shows that **both single-queue and multi-queue approaches can be viable**, depending on system goals and trade-offs.

---

### O(1) Scheduler
- Uses **multiple queues**
- **Priority-based** scheduler (similar to MLFQ)
- Scheduler decisions take constant time → **O(1)**
- Adjusts process priorities dynamically over time
- Focuses heavily on **interactivity** (keeping the system responsive)

**Key idea:**  
Run the highest-priority task next, quickly and predictably.

---

### Completely Fair Scheduler (CFS)
- Uses **multiple queues**
- A **deterministic proportional-share scheduler**
- Conceptually similar to **stride scheduling**
- Tries to divide CPU time fairly among processes
- Uses the idea of *virtual runtime* to approximate “ideal multitasking”

**Key idea:**  
Each process should receive its fair share of CPU time over the long run.

> CFS is the **default scheduler in modern Linux systems**.

---

### Brain Fuck Scheduler (BFS)
- Uses a **single queue**
- Still a **proportional-share scheduler**
- Based on **Earliest Eligible Virtual Deadline First (EEVDF)**
- Much simpler design than O(1) or CFS
- Often targets **desktop responsiveness**

**Key idea:**  
A single global queue can work if fairness is computed carefully.

---

### Comparison Summary

| Scheduler | Queue Structure | Scheduling Style | Main Focus |
|--------|----------------|------------------|-----------|
| O(1) | Multi-queue | Priority-based | Interactivity |
| CFS | Multi-queue | Proportional-share | Fairness |
| BFS | Single-queue | Proportional-share (EEVDF) | Simplicity + fairness |

---

### Key Takeaways (Exam-Oriented)

- Linux has experimented with **multiple multiprocessor schedulers**
- **Multi-queue and single-queue designs can both succeed**
- O(1) emphasizes **priority and responsiveness**
- CFS emphasizes **fair CPU sharing**
- BFS shows that even **single-queue schedulers** can be fair
- Scheduler design depends on trade-offs between **fairness, scalability, and simplicity**

**Exam sentence to remember:**

> Linux multiprocessor scheduling evolved through multiple designs (O(1), CFS, BFS), demonstrating that both single-queue and multi-queue schedulers can work depending on system goals such as fairness and interactivity.

---
---

# Chapter 10 — Multiprocessor Scheduling (Advanced)

## Big Picture

This chapter explains **why CPU scheduling becomes much harder when multiple CPUs are involved**.  
While hardware guarantees *correctness* (via cache coherence), **performance, scalability, and fairness** become difficult to balance. There is **no single best scheduler** — only trade-offs.

---

## Core Problems in Multiprocessor Scheduling

### 1. Cache Coherence vs. Cache Performance
- Hardware cache coherence ensures **correctness**.
- However, **performance suffers** if a process frequently moves between CPUs.
- Each CPU has its own cache and TLBs.
- Moving a process forces cache reloads → slower execution.

**Key concept:** **Cache Affinity**  
> A process should preferably run on the same CPU it ran on before.

---

### 2. Synchronization Is Unavoidable
- Shared data structures (run queues, lists, counters) exist.
- Even with cache coherence, **locks are required** for correctness.
- Locks introduce:
  - Contention
  - Overhead
  - Poor scalability as CPU count increases

**Takeaway:**  
Correctness requires synchronization, but synchronization hurts scalability.

---

## Scheduling Approaches

### 10.4 Single-Queue Multiprocessor Scheduling (SQMS)

**Idea:**
- One global run queue
- All CPUs pull jobs from the same queue

**Advantages:**
- Simple design
- Automatic load balancing

**Problems:**
- Heavy lock contention
- Poor scalability
- Bad cache affinity (processes bounce between CPUs)

**Summary:**
> SQMS is easy to implement but does **not scale**.

---

### 10.5 Multi-Queue Multiprocessor Scheduling (MQMS)

**Idea:**
- One run queue per CPU
- Each CPU schedules independently

**Advantages:**
- Scales well
- Preserves cache affinity
- Minimal locking

**New Problems Introduced:**
- Load imbalance (one CPU idle, another overloaded)
- Requires **process migration**
- Migration destroys cache affinity

---

## Load Imbalance and Migration

### Load Imbalance
Occurs when:
- Some CPUs have many jobs
- Others have few or none

### Migration
- Moving a job from one CPU to another to rebalance load

**Benefits:**
- Improves utilization
- Prevents idle CPUs

**Costs:**
- Cache and TLB cold misses
- Performance degradation

---

## Work Stealing

**Work Stealing Strategy:**
- An idle CPU looks at another CPU’s queue
- “Steals” one or more jobs

**Trade-off:**
- Too frequent stealing → high overhead
- Too rare stealing → load imbalance

**Important Insight:**
> Finding the right migration frequency is a *heuristic*, not a formula.

---

## Cache Affinity (10.3)

- Processes accumulate state in CPU caches and TLBs.
- Running on the same CPU is faster.
- Schedulers try to:
  - Preserve affinity when possible
  - Break it only to improve load balance

---

## Linux Multiprocessor Schedulers (10.6)

Linux does **not** use a single pure model.

Historically:
- **O(1) Scheduler**: Priority-based, MLFQ-like
- **CFS (Completely Fair Scheduler)**: Deterministic proportional-share
- **BFS**: Single-queue proportional-share (EEVDF-based)

Modern Linux:
- Uses **per-CPU run queues**
- Preserves cache affinity
- Performs periodic load balancing
- Applies heuristics instead of strict rules

**Key lesson:**
> Real systems mix ideas instead of following theory strictly.

---

## Why There Is No “Perfect” Scheduler

Every improvement introduces a cost:

| Goal | Helps | Hurts |
|----|----|----|
| Single queue | Load balance | Scalability, cache affinity |
| Multiple queues | Scalability | Load balance |
| Migration | Utilization | Cache locality |
| Cache affinity | Performance | Fairness |

**Final takeaway:**
> Multiprocessor scheduling is about trade-offs, not optimal solutions.

---

## Exam-Oriented Key Takeaways

You should be able to explain:

- Why multiprocessor scheduling is harder than single-CPU scheduling
- What cache affinity is and why it matters
- Why a single global queue does not scale
- Why per-CPU queues cause load imbalance
- What migration solves and what it breaks
- Why general-purpose schedulers rely on heuristics

---

## Final Summary (My Added Summary)

Multiprocessor scheduling highlights the fundamental tension between **scalability, fairness, cache performance, and simplicity**.  
Single-queue schedulers are simple but fail to scale; multi-queue schedulers scale but require complex migration policies. Cache affinity improves performance but conflicts with load balancing. As a result, modern operating systems (like Linux) rely on **hybrid designs and heuristics**, accepting that no scheduler can optimize all goals simultaneously.












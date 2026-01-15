Chapters 7,8,9,10,

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




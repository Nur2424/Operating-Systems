1.
The transition from user to kernel mode occurs when:
Choose an alternative:
A. The first instruction of a program is executed
B. The time slice assigned to the currently executing process elapses
C. A program makes a function call
D. The system is starting up (bootstrap)

⸻

2.
The operating system:
Choose an alternative:
A. Is stored in main memory (RAM) even after the machine is shut down
B. Must obey the CPU scheduling policies as any other user process does
C. Represents the interface between the physical machine (i.e., hardware) and user applications
D. Coincides exactly with the kernel

⸻

3.
The system calls:
Choose an alternative:
A. Must be implemented in kernel space
B. Are always blocking (i.e., they always suspend the process that makes them)
C. Must be implemented in user space
D. Cause the termination of the currently executing process and the starting of a new one

⸻

4.
The system call handler:
Choose an alternative:
A. Manages system calls using the system call table
B. Is called by the CPU scheduler
C. Is invoked every time the quantum slice elapses
D. Is executed in user space

⸻

5.
A running process on the CPU moves to the waiting status when:
Choose an alternative:
A. It makes a function call
B. It gets an interrupt signal from an I/O device
C. Its time slice elapses
D. It asks for opening a network connection (e.g., a TCP socket)

⸻

6.
A preemptive scheduler takes over when:
Choose an alternative:
A. A process moves from running to waiting
B. A process moves from running to ready
C. A process moves from waiting to ready
D. All the previous answers are correct

⸻

7.
Compute the average waiting time of the following processes, assuming a round robin CPU scheduling policy with time slice = 3, no I/O requests and negligible time needed for context switch:
(Processes table: A(0,5), B(2,8), C(7,4), D(8,3))
Choose an alternative:
A. 5.85
B. 6.5
C. 7.15
D. 6.75

⸻

8.
In the many-to-one thread mapping:
Choose an alternative:
A. Several user threads are mapped on a single kernel thread
B. Several kernel threads are mapped on a single user thread
C. Several user threads can be distributed across more CPUs (if possible)
D. A blocking call from a user thread does not block the other threads (of the same process)

⸻

9.
To use a lock synchronisation primitive, we must ensure that:
Choose an alternative:
A. The lock is initially free
B. The lock is acquired before entering the critical section (of code)
C. The lock is released after exiting the critical section (of code)
D. All the above conditions must be verified

⸻

10.
The acquisition of a lock:
Choose an alternative:
A. Must be done atomically so as to avoid the CPU scheduler takes over in the middle
B. Can only be implemented by atomic hardware instructions
C. Always requires that the operating system disables interrupts
D. None of the above answers is correct

⸻

11.
The term address binding refers to:
Choose an alternative:
A. The translation from logical to physical addresses
B. The initialization of the global variables of a program
C. The linking of compiled code with any external library referenced
D. None of the above answers is correct

⸻

12.
Suppose a process whose size is 2,097 bytes and a free block of main memory has size 2,104 bytes. Assuming contiguous memory allocation, the most convenient choice is to:
Choose an alternative:
A. Wait for a smaller block that best fits the process
B. Allocate only the portion of the block that is necessary to the process and add to the list of free blocks the remaining 7 bytes (external fragmentation)
C. Allocate the whole block to the process, thereby wasting 7 bytes (internal fragmentation)
D. Wait for a larger block whose size is multiple of that of the process

⸻

13.
Suppose you have a memory M whose size is 8 KiB, namely 8,192 bytes. If the smallest addressable unit corresponds to a word size equals to a single byte and M is divided into pages, each one with size S = 128 bytes, what is the dimension (i.e., the number of entries) of the corresponding page table T?
Choose an alternative:
A. There is not enough information to answer this question
B. 7
C. 13
D. 64

⸻

14.
The virtual memory allows to:
Choose an alternative:
A. Increment the efficiency of I/O operations
B. Lower the degree of multiprogramming
C. Execute a process directly from secondary storage (e.g., disk)
D. Keep in main memory only some of the pages of the whole virtual address space of a process

⸻

15.
Suppose the access time to physical memory is t_{MA} = 50 nsec. and the time for handling a page fault is t_{FAULT} = 15 msec. Assuming the probability of page fault is p = 0.0002, what is the total time to access physical memory?
Choose an alternative:
A. ~3.05 microsec
B. ~30.5 microsec
C. ~30.5 nsec
D. ~305 nsec

⸻

16.
Consider a main memory made of 3 (physical) frames and a process spawning 5 (virtual) pages: A, B, C, D, E. Calculate the number of page faults that occur upon the following sequence of page requests issued by the process: A, B, E, C, E, D, D, A, B. We assume that, initially, no page is loaded in main memory and the system uses a FIFO page replacement algorithm.
Choose an alternative:
A. 7
B. 6
C. 8
D. 4

⸻

17.
In a magnetic (rotational) disk, the seek time:
Choose an alternative:
A. Is the time required to the disk for placing their head over a specific cylinder
B. Is negligible with respect to the whole time needed for transferring data (to main memory)
C. Includes the time for transferring data to main memory
D. Is the time required to the disk for placing their head over a specific sector

⸻

18.
Let us consider a magnetic (rotational) disk composed of 128 cylinders/tracks, numbered from 0 to 127 (where 0 is the index of the outermost cylinder/track), whose head is initially over cylinder 42. Calculate the total number of cylinders/tracks traversed by the head, assuming the following sequence of requests (74, 50, 32, 55, 81) is managed by an SSTF (Shortest Seek Time First) disk scheduling algorithm, without considering the rotational delay.
Choose an alternative:
A. 49
B. 123
C. 86
D. 88

⸻

19.
Contiguous allocation of file on disk:
Choose an alternative:
A. Is optimal both for direct (i.e., random) and sequential access
B. Suffers from the problem of fragmentation
C. Needs to keep track of the free blocks using a dedicated data structure
D. All the above answers are correct

⸻

20.
A possible example of an application that needs sequential access to a file is:
Choose an alternative:
A. A compiler
B. A search engine within a database system
C. A search engine within a contact list
D. None of the above answers are correct

⸻

Below are extracted from the second batch (new set of question numbers), included as additional exercises starting after 20.

⸻

21.
The so-called stack frame (a.k.a. activation record):
Select one:
A. Contains at least the arguments of a function and the address of the instruction that precedes its call
B. Contains at least the arguments of a system call and the address of the instruction that follows its call
C. Contains at least the arguments of a system call and the address of the instruction that follows its call
D. Contains at least the arguments of a function and the address of the instruction that follows its call

⸻

22.
With port-mapped I/O the CPU interacts with I/O devices:
Select one:
A. Using normal main memory (RAM) addresses
B. Using dedicated registers
C. Using a portion of secondary memory addresses
D. Using a first-level (L1) memory cache

⸻

23.
Let foo(arg1, …, argN) a function that wraps a system call. Assuming N=2, what is the best strategy for parameter passing?
Select one:
A. Using pass-by-reference
B. Using pass-by-value
C. Using a dedicated and protected area of main memory
D. Using internal CPU registers

⸻

24.
Consider a multicore CPU having m cores; at any given time, the number of processes/threads that are in the ready queue(s):
Select one:
A. Is at most equal to m
B. There is not enough data to answer this question
C. Can be larger than m
D. Must be strictly less than m

⸻

25.
When a parent process creates a new child process using fork(), what of the following information will be shared between the two processes?
Select one:
A. Stack
B. Heap
C. Shared memory segments
D. None of the above answers is correct

⸻

26.
If a process gets to the ready queue at time t₀ = 2 and terminates at t_f = 16, its completion time equals to:
Select one:
A. 16
B. 14
C. 18
D. There is not enough data to answer this question

⸻

27.
Let us consider 5 processes as illustrated in the picture below. What is the turnaround time of process E using Shortest Job First (SJF) scheduling?
Table: (A:0,2), (B:0,1), (C:0,8), (D:0,4), (E:0,5)
Select one:
A. 7
B. 12
C. 20
D. There is not enough data to answer this question

⸻

28.
Threads of the same process share:
Select one:
A. The stack memory segment
B. The values of the CPU registers
C. Global variables
D. All the above answers are correct

⸻

29.
The so-called many-to-many thread mapping model:
Select one:
A. Does not assume any limit on the number of possible kernel threads
B. Causes all the threads of a process to block even if just one of them makes a blocking system call
C. Is a compromise between a purely user level and a purely kernel level implementation
D. Can only be implemented on multi-processor systems

⸻

30.
Calling the method wait() on a semaphore whose value is 2:
Select one:
A. Decrements the value of the semaphore to 1 and lets the process/thread that executed the call continue running
B. Increments the value of the semaphore to 3 and lets the process/thread that executed the call continue running
C. Decrements the value of the semaphore to 1 and blocks the process/thread that executed the call
D. Leaves the value of the semaphore to 2 and lets the process/thread that executed the call continue running

⸻

31.
Let us consider 2 processes/threads Producer (P) and Consumer (C) whose code is made of the following instructions:
P: (P1) R1 = val; (P2) R1 += 1; (P3) val = R1;
C: (C1) R2 = val; (C2) R2 += 1; (C3) val = R2;
Assuming R1 and R2 are internal CPU registers and val is a variable stored in main memory whose value is initially set to 1, what will be the value stored in val after P and C terminate, if the overall instructions (of P and C) are scheduled in the following order: C1, C2, P1, P2, P3, C3?
Select one:
A. 41
B. 42
C. 43
D. There is not enough data to answer this question

⸻

32.
When address binding is done at compile time:
Select one:
A. Physical addresses correspond to logical addresses
B. A hardware support is further needed to implement the address translation
C. Compiled code is portable to any platform
D. The compiled code is called relocatable

⸻

33.
Let us consider a process whose size is 4,096 bytes and a free block of memory whose size is 7,214 bytes. If we assume memory must be allocated contiguously, the most convenient choice in this case is to:
Select one:
A. Allocate the entire block to the process, thereby wasting 3,118 bytes (internal fragmentation)
B. Wait for a smaller free block that can contain the process
C. Allocate only the portion needed by the process, and keep track of the remaining 3,118 bytes in the list of free blocks (external fragmentation)
D. Wait for a free block whose size is an integer multiple of the size of the process

⸻

34.
Consider a memory M whose size is 128 KiB, namely 131,072 bytes. Assuming the word size is 8 bytes (i.e., the smallest addressable unit is 8 bytes) and M uses paging where each page has size S = 2 KiB = 2,048 bytes, how much space is needed to store the corresponding page table T, assuming each entry is represented with a 64-bit integer?
Select one:
A. 2 KiB
B. 1 KiB
C. 512 B
D. 256 B

⸻

35.
A page fault occurs when a process:
Select one:
A. Tries to access a critical section of code
B. Generates a logical address that maps to a frame which is not loaded in main memory
C. Tries to execute a division by zero
D. Makes an I/O call

⸻

36.
Consider a system that implements an approximated LRU page replacement algorithm using the second chance heuristic (i.e., clock). When a page fault occurs, the heuristic scans the list of frames and if it finds a frame whose reference bit is set to 1:
Select one:
A. The reference bit is left to 1 and the frame is replaced with the page that caused the page fault
B. The reference bit is set to 0 and the frame is replaced with the page that caused the page fault
C. The reference bit is set to 0 and the heuristic moves to the next item of the list
D. The reference bit is left to 1 and the heuristic moves to the next item of the list

⸻

37.
The total transfer time for an I/O operation involving a magnetic disk is equal to 25 msec. Suppose that: the seek time is equal to 10 msec, the rotational delay is equal to 3 msec, and the transfer rate is equal to 4 Gbit/sec, what is the total amount of data transferred? (Note: 1 B = 1 byte = 8 bit and 1 MB = 10^3 KB = 10^6 B)
Select one:
A. There is not enough data to answer this question
B. 48 MB
C. 60 MB
D. 6 MB

⸻

38.
Consider a rotational magnetic disk composed of 128 cylinders/tracks, numbered from 0 to 127 (0 being the index of the outermost cylinder/track). Initially, the head is over cylinder 63. Calculate the number of cylinders/tracks traversed by the head, assuming the following sequence of requests (15, 84, 55, 104, 71) are managed by a non-optimized C-SCAN disk scheduling algorithm with the head moving outwards (i.e., towards lower cylinder numbers), disregarding the rotational delay.
Select one:
A. 104
B. 246
C. 134
D. 231

⸻

39.
The transition from user to kernel mode occurs when:
Choose an alternative:
a. The time slice assigned to the currently executing process elapses
b. A program makes a function call
c. The first instruction of a program is executed
d. The system is starting up (bootstrap)

⸻

40.
The operating system:
Choose an alternative:
a. Represents the interface between the physical machine (i.e., hardware) and user applications
b. Must obey the CPU scheduling policies as any other user process does
c. Is stored in main memory (RAM) even after the machine is shut down
d. Coincides exactly with the kernel

⸻

41.
The system calls:
Choose an alternative:
a. Must be implemented in kernel space
b. Must be implemented in user space
c. Are always blocking (i.e., they always suspend the process that makes them)
d. Cause the termination of the currently executing process and the starting of a new one

⸻

42.
The system call handler:
Choose an alternative:
a. Manages system calls using the system call table
b. Is invoked every time the quantum slice elapses
c. Is called by the CPU scheduler
d. Is executed in user space

⸻

43.
A running process on the CPU moves to the waiting status when:
Choose an alternative:
a. Its time slice elapses
b. It asks for opening a network connection (e.g., a TCP socket)
c. It makes a function call
d. It gets an interrupt signal from an I/O device

⸻

44.
A preemptive scheduler takes over when:
Choose an alternative:
a. A process moves from running to waiting
b. A process moves from running to ready
c. A process moves from waiting to ready
d. All the previous answers are correct

⸻

45.
Compute the average waiting time of the following processes, assuming a round robin CPU scheduling policy with time slice = 3, no I/O requests and negligible time needed for context switch:
Jobs: A(0,5), B(2,8), C(7,4), D(8,3)
Choose an alternative:
a. 6.75
b. 5.85
c. 7.15
d. 6.5

⸻

46.
In the many-to-one thread mapping:
Choose an alternative:
a. Several user threads are mapped on a single kernel thread
b. A blocking call from a user thread does not block the other threads (of the same process)
c. Several user threads can be distributed across more CPUs (if possible)
d. Several kernel threads are mapped on a single user thread

⸻

47.
To use a lock synchronisation primitive, we must ensure that:
Choose an alternative:
a. The lock is initially free
b. The lock is acquired before entering the critical section (of code)
c. The lock is released after exiting the critical section (of code)
d. All the above conditions must be verified

⸻

48.
The acquisition of a lock:
Choose an alternative:
a. Must be done atomically so as to avoid the CPU scheduler takes over in the middle
b. Can only be implemented by atomic hardware instructions
c. Requires always that the operating system disables interrupts
d. None of the above answers is correct

⸻

49.
The term address binding refers to:
Choose an alternative:
a. The translation from logical to physical addresses
b. The initialization of the global variables of a program
c. The linking of compile code with any external library referenced
d. None of the above answers is correct

⸻

50.
Suppose a process whose size is 2,097 bytes and a free block of main memory has size 2,104 bytes. Assuming contiguous memory allocation, the most convenient choice is to:
Choose an alternative:
a. Wait for a smaller block that best fits the process
b. Allocate the whole block to the process, thereby wasting 7 bytes (internal fragmentation)
c. Allocate only the portion of the block that is necessary to the process and add to the list of free blocks the remaining 7 bytes (external fragmentation)
d. Wait for a larger block whose size is multiple of that of the process

⸻

51.
Suppose you have a memory M whose size is 8 KiB, namely 8,192 bytes. If the smallest addressable unit corresponds to a word size equals to a single byte and M is divided into pages, each one with size S = 128 bytes, what is the dimension (i.e., the number of entries) of the corresponding page table T?
Choose an alternative:
a. There is not enough information to answer this question
b. 7
c. 13
d. 64

⸻

52.
Suppose the access time to physical memory is tₘₐ = 50 nsec and the time for handling a page fault is t_fault = 15 msec. Assuming the probability of page fault is p = 0.0002, what is the total time to access physical memory?
Choose an alternative:
a. ~3.05 microsec
b. ~30.5 microsec
c. ~30.5 nsec
d. ~305 nsec

⸻

53.
In a magnetic (rotational) disk, the seek time:
Choose an alternative:
a. Is the time required to the disk for placing their head over a specific cylinder
b. Includes the time for transferring data to main memory
c. Is negligible with respect to the whole time needed for transferring data (to main memory)
d. Is the time required to the disk for placing their head over a specific sector

⸻

54.
The virtual memory allows to:
Choose an alternative:
a. Lower the degree of multiprogramming
b. Lower the degree of multiprogramming
c. Increment the efficiency of I/O operations
d. Keep in main memory only some of the pages of the whole virtual address space of a process

⸻

55.
Consider a main memory made of 3 (physical) frames and a process spawning 5 (virtual) pages: A, B, C, D, E. Calculate the number of page faults that occur upon the following sequence of page requests issued by the process: A, B, E, C, E, D, D, A, B. We assume that, initially, no page is loaded in main memory and the system uses a FIFO page replacement algorithm.
Choose an alternative:
a. 6
b. 7
c. 8
d. 4

⸻

56.
Let us consider a magnetic (rotational) disk composed of 128 cylinders/tracks, numbered from 0 to 127 (where 0 is the index of the outermost cylinder/track), whose head is initially over cylinder 42. Calculate the total number of cylinders/tracks traversed by the head, assuming the following sequence of requests (74, 50, 32, 55, 81) is managed by an SSTF disk scheduling algorithm, without considering the rotational delay.
Choose an alternative:
a. 49
b. 123
c. 86
d. 88

⸻

57.
Considering the following code snippet, indicate the corresponding tree of processes generated (Note: by convention, assume that each level of the tree is generated from left to right):
(contains code snippet with forks)
Choose an alternative:
a. [tree diagram]
b. [tree diagram]
c. [tree diagram]
d. [tree diagram]

⸻

58.
Compute the average waiting time of the following processes under the assumption of a First Come First Served (FCFS) scheduling policy. Moreover, at t=2, process A issues an I/O request that will be completed after 4 time units, i.e., at t=6. The time that it takes for each context switch is assumed to be negligible (i.e., approximately 0), and therefore must not be included in the computation of the average waiting time:
Jobs: A(0,5), B(1,3), C(3,7), D(6,3)
Choose an alternative:
a. 5.5
b. 7.5
c. 6.5
d. 4.5

⸻

59.
CPU-bound processes that do not execute any I/O request:
Choose an alternative:
a. Could never voluntarily release the CPU
b. Are typically “short” processes (i.e., they terminate very quickly)
c. Are “short” processes (i.e., they terminate very quickly)
d. Have a low priority

⸻

60.
The communication between threads of the same process with respect to that amongst different processes:
Choose an alternative:
a. Is faster because threads (of the same process) do not execute context switch
b. Is roughly the same and no one outperforms the other
c. Is slower because threads (of the same process) are managed by high-level libraries
d. Is faster because threads (of the same process) share the same virtual address space

⸻

61.
The term concurrency is used when:
Choose an alternative:
a. Single-threaded processes are executed on multi-core CPUs
b. Multi-threaded processes are executed on multi-core CPUs
c. Single-threaded processes are executed on single-core CPUs
d. Multi-threaded processes are executed on single-core CPUs

⸻

62.
Calling the method wait() on a semaphore whose value is 2:
Choose an alternative:
a. Decrements the value of the semaphore to 1 and blocks the process/thread that executed the call
b. Increments the value of the semaphore to 3 and lets the process/thread that executed the call continue running
c. Decrements the value of the semaphore to 1 and lets the process/thread that executed the call continue running (conditioned on the CPU scheduling policies)
d. Leaves the value of the semaphore to 2 and lets the process/thread that executed the call continue running (conditioned on the CPU scheduling policies)

⸻

63.
A compiler generates the virtual (i.e., logical) address 576 to access a certain physical memory location. Assuming that address binding is performed via static relocation with base physical address b=24, what will be the final physical address referenced?
Select one:
a. 600
b. 552
c. There is not enough data to answer this question
d. 576

⸻

64.
Suppose a process P requires a block of free memory of 99 KiB in order to be contiguously allocated in main memory. The list of free blocks contains the following items: A, B, C, D, whose size are respectively: 102 KiB, 99 KiB, 256 KiB, and 128 KiB. Which free block will be allocated for P, assuming a Worst-Fit allocation policy?
Choose an alternative:
a. A
b. B
c. C
d. D

⸻

65.
The following Resource Allocation Graph (RAG) indicates a system that:
Choose an alternative:
a. Depends on the choices made by the CPU scheduler
b. Is presenting a deadlock
c. Is “not” presenting any deadlock
d. It is impossible to answer

⸻

66.
The size (i.e., the number of entries) of the page table:
Choose an alternative:
a. Changes dynamically from process to process
b. Is directly proportional to the (fixed) page size
c. Adapts to the memory requests made by each process
d. Depends on the (fixed) page size

⸻

67.
Consider a memory M whose size is 4 KiB, namely 4,096 bytes. Assuming the word size is 2 bytes (i.e., the smallest addressable unit is 2 bytes) and M uses paging where each page has size S = 128 bytes, how many bits are necessary for specifying the page number (p) and the offset (within each page), respectively?
Choose an alternative:
a. p=7, o=5
b. p=5, o=7
c. p=6, o=5
d. p=5, o=6

⸻

68.
The total transfer time for an I/O operation involving a magnetic disk is equal to 30 msec. Suppose that: the seek time is equal to 18 msec, the rotational delay is equal to 7 msec, and the transfer rate is equal to 15 Gbit/sec. What is the total amount of data transferred? (Note: 1 B = 1 byte = 8 bit and 1 MB = 10^3 KB = 10^6 B)
Choose an alternative:
a. 937.5 MB
b. 7.5 MB
c. 937.5 KB
d. There is not enough data to answer this question

⸻

69.
Consider the memory access time is tₘₐ = 60 nsec and the time for handling a page fault is t_fault = 5 msec. What will be the (upper-bound) value of the probability of page fault (p) if we want to guarantee that the expected memory access time is at most 20% slower than tₘₐ? (Note: 1 msec = 10^3 microsec = 10^6 nsec)
Choose an alternative:
a. There is not enough data to answer this question
b. ~0.00024%
c. ~0.000024%
d. ~0.0000024%

⸻

70.
Consider a memory composed of 3 physical frames and a process made of 5 logical pages: A, B, C, D, E. Calculate the number of page faults upon the following sequence of memory access requests issued by the process: B, C, C, B, A, C, B, B, A, C, E, D, B. At the beginning, no page is loaded in memory and LRU page replacement is used.
Choose an alternative:
a. 7
b. 5
c. 6
d. 4

⸻

71.
Consider a rotational magnetic disk composed of 128 cylinders/tracks, numbered from 0 to 127 (0 being the index of the outermost cylinder/track). Initially, the head is over cylinder 87. Calculate the number of cylinders/tracks traversed by the head, assuming the following sequence of requests (43, 81, 36, 25, 126, 27) are managed by a First Come First Served (FCFS) disk scheduling algorithm, disregarding the rotational delay.
Choose an alternative:
a. 87
b. 44
c. 238
d. 240

⸻

72.
In a UNIX-like system, a file having the following privilege: 101000000:
Choose an alternative:
a. Does not give to the owner of the file any right at all
b. Allows only to the owner of the file to write and execute it
c. Allows only to the owner of the file to read and execute it
d. Allows only to the owner of the file to read it and write it

⸻

73.
Consider a file system organized with multi-level indexed files. The file descriptor contains direct references to 10 blocks of data; additionally, the 11th field of the file descriptor is a pointer to an array of 100 blocks, and the 12th field is a pointer to an array of 100 pointers, each one referencing 100 blocks. If each data block size is 2 KiB, what is the maximum dimension supported by this file system?
Choose an alternative:
a. ~20.2 MB
b. ~20.2 KB
c. ~20.2 KiB
d. ~20.7 MB

⸻

74.
A disk is made of 15 cylinders, each one of size 500 MB. What is the total capacity of the disk?
Choose an alternative:
a. 7.5 GB
b. 75 GB
c. 750 MB
d. There is not enough data to answer this question

⸻

75.
Main memory managed with paging:
Choose an alternative:
a. Assumes the logical address space of a process is contiguous and divided into fixed-size blocks (i.e., pages)
b. Does not require any hardware support for it to be implemented efficiently
c. Assumes the physical address space of a process is contiguous and divided into fixed-size blocks (i.e., frames)
d. Fixes the issue of internal fragmentation

⸻

76.
Consider a memory M whose size is 512 bytes, where each frame has size 16 bytes. Given the byte address 197, what is the page number (p) and the offset (within that page)?
Choose an alternative:
a. p=5; offset=12
b. p=13; offset=0
c. There is not enough data to answer this question
d. p=12; offset=5

⸻

77.
If an idempotent instruction generates a page fault:
Choose an alternative:
a. The instruction will be executed again from scratch once the page fault is handled
b. The instruction will never be executed again from scratch once the page fault is handled
c. The process that attempted the execution of the instruction will be stopped
d. Idempotent instructions cannot generate page fault

⸻

78.
Consider a rotational magnetic disk composed of 100 cylinders/tracks, numbered from 0 to 99 (0 being the index of the outermost cylinder/track). Initially, the head is over cylinder 11. Calculate the number of cylinders/tracks traversed by the head, assuming the following sequence of requests (24, 16, 77, 49, 82) are managed by a non-optimized SCAN disk scheduling algorithm with the head moving outwards (i.e., towards lower cylinder numbers), disregarding the rotational delay.
Choose an alternative:
a. 82
b. 44
c. 93
d. 71

⸻

79.
The global open file table:
Choose an alternative:
a. Is shared amongst all the processes
b. Contains an entry for each file currently in use
c. Keeps a counter for each file currently in use
d. All the above answers are correct

⸻

80.
In a UNIX-like system, a file having the following privileges: 011000000:
Choose an alternative:
a. Allows only to the owner of the file to read it and execute it
b. Does not give to the owner of the file any right at all
c. Allows only to the owner of the file to write it and execute it
d. Allows only to the owner of the file to read it and write it

⸻

81.
Consider a system that uses 21-bit long virtual addresses, 16-bit long physical addresses, and memory paging where each page has size 2 KiB = 2,048 bytes. What is the maximum size of physical memory supported by the system?
Choose an alternative:
a. 32 KiB
b. 64 KiB
c. 2 MiB
d. There is no limit to the maximum size of physical memory supported

⸻

82.
Consider a system that implements an approximated LRU page replacement algorithm using the second chance heuristic (i.e., clock). When a page fault occurs, the heuristic scans the list of frames and if it finds a frame whose reference bit is set to 1:
Choose an alternative:
a. The reference bit is left to 1 and the heuristic moves to the next item of the list
b. The reference bit is set to 0 and the heuristic moves to the next item of the list
c. The reference bit is set to 0 and the frame is replaced with the page that caused the page fault
d. The reference bit is left to 1 and the frame is replaced with the page that caused the page fault

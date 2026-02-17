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

---

1. The operating system:
A. Coincides with the kernel
B. Acts as the interface between the physical machine (hardware) and user applications
C. Is subject to scheduling policies
D. Resides in main memory even after the machine is shut down

2. In a microkernel operating system:
A. Some functionalities are implemented in user space instead of inside the kernel
B. User processes can interact directly with the system without system calls
C. Communication between system components is more efficient
D. No protection mechanisms are provided

3. In a microkernel-based operating system:
A. It does not need two CPU modes (user vs kernel mode)
B. It does not need communication mechanisms between OS components
C. It is more efficient than a monolithic system
D. Except for fundamental functionalities, everything else runs in user space

4. The machine-level instruction set:
A. Is composed of an operation code and zero or more operands
B. Is defined by a specific machine language
C. Is an abstraction of the hardware architecture
D. All the previous answers are correct

5. CPU registers and cache are memory units:
A. Non-volatile
B. Fully managed by hardware architecture
C. Fully managed by the OS
D. Very cheap and highly performant

6. Transition from user mode to kernel mode happens when:
A. A program executes a function call
B. The computer starts (bootstrap)
C. The first instruction of a program executes
D. The time slice expires

7. The device controller of an I/O device:
A. Contains status registers
B. Contains control registers
C. Contains data registers
D. All the previous answers are correct

8. System calls:
A. Are always blocking
B. Terminate the running process and start a new one
C. Must be implemented in user space
D. Must be implemented in kernel space

9. A blocking system call:
A. Moves process to ready queue
B. Permanently interrupts the process
C. Temporarily interrupts the process
D. Requires polling

10. The system call handler:
A. Invoked by scheduler
B. Invoked when quantum expires
C. Runs in user space
D. Uses the system call table

11. Generic system call handler code:
A. Runs in user space
B. Indexed via interrupt vector table
C. Invoked when quantum expires
D. Invoked by scheduler

12. Interrupt Vector Table (IVT):
A. Updated at every interrupt
B. Contains pointers to interrupt handlers
C. Exists per process
D. Contains error codes

13. The system call table:
A. Contains entries equal to number of system calls
B. Entries equal to interrupts
C. Entries equal to I/O devices
D. Entries equal to running processes

14. The system call table is managed by:
A. I/O devices
B. User process
C. Kernel and user process
D. Kernel

15. If a system call implementation changes:
A. User code must always change
B. User code never changes
C. No change if API changes
D. No change if API stays the same

16. CPU CPI = 5, frequency = 5 MHz. Instructions/sec?
A. 1×10³
B. I choose not to answer
C. 25×10³
D. 1×10⁶
E. 25×10⁶

17. On an m-core CPU, running processes at a time:
A. Can exceed m
B. Exactly m
C. Insufficient data
D. At most m

⸻

Processes

18. Process creation happens via:
A. System call
B. Function call
C. Interrupt
D. None

19. OS tracks process state using:
A. Protected memory area
B. CPU register
C. Cache memory area
D. PCB field

20. Running → Ready when:
A. I/O interrupt arrives
B. User input request
C. Page fault occurs
D. Function call

21. Running → Waiting when:
A. I/O signal arrives
B. Quantum expires
C. Open TCP connection
D. Function call

22. Running → Waiting when:
A. Requests user input
B. Function call
C. Quantum expires
D. I/O interrupt

23. Running → Waiting when:
A. Quantum expires
B. Mouse moves
C. Function call
D. I/O interrupt

24. After three fork() calls, how many processes?
A. 8
B. 7
C. 4
D. 3

25. Value printed by given code snippet:
A. 5
B. 20
C. 15
D. Insufficient data

26. Process tree from given code.
A. Option A
B. Option B
C. Option C
D. Option D

27. Process tree from given code.
A. Option A
B. Option B
C. Option C
D. Option D

28. CPU-bound processes:
A. High priority
B. Low priority
C. Short processes
D. May never release CPU voluntarily

⸻

Scheduling

29. CPU scheduler activates when:
A. Writing on Discord
B. Division by zero
C. Quantum expires
D. All answers

30. Preemptive scheduling:
A. Prioritizes CPU-bound
B. Only at quantum expiry
C. Only after system call
D. Guarantees CPU time bound

⸻

Scheduling (continued)

31. In a single-core time-sharing system with only CPU-bound processes:
A. Multithreading improves latency
B. Multithreading decreases completion time
C. Multithreading improves throughput
D. Multithreading gives no advantage

32. With preemptive scheduling, the scheduler runs when:
A. Running → Waiting
B. Running → Ready
C. Waiting → Ready
D. All the previous answers

33. A process arrives at t₀ = 2 and finishes at t_f = 15. Turnaround time:
A. 13
B. 2
C. Insufficient data
D. 15

34. A process arrives at t₀ = 3 and finishes at t_f = 25. Waiting time:
A. 3
B. 22
C. 25
D. Insufficient data

35. Given processes and scheduling policies (FCFS, SJF, RR q=2), which gives the smallest waiting time for process C?
A. FCFS
B. RR
C. SJF
D. All equal

36. Average waiting time with Round Robin (q = 3):
A. 6.5
B. 6.75
C. 7.15
D. 5.85

37. Average waiting time with Round Robin (q = 4):
A. 4.85
B. 4.25
C. 4.5
D. 4.75

38. Average waiting time with SJF preemptive:
A. 6
B. 5.75
C. 4.5
D. 5

39. Average waiting time with FCFS and I/O by process A:
A. 4.5
B. 5.5
C. 7.5
D. 6.5

40. Average waiting time with FCFS and I/O by process B:
A. 4.5
B. 5.25
C. 4
D. 4.25

⸻

Threads and Synchronization

41. Threads of the same process share:
A. Stack
B. Global variables
C. CPU registers
D. None

42. A user thread:
A. Requires kernel thread table
B. Smallest unit scheduled by OS
C. Managed in user space via library
D. Always maps to one kernel thread

43. One-to-one thread mapping:
A. Managed by user library
B. Only on multiprocessors
C. Blocking call blocks all threads
D. Managed by kernel

44. Many-to-one thread mapping:
A. Many user threads run on multiple CPUs
B. Blocking call doesn’t block others
C. Many user threads map to one kernel thread
D. Many kernel threads map to one user thread

45. Many-to-many thread mapping:
A. No limit on kernel threads
B. Only multiprocessors
C. Blocking call blocks all threads
D. Hybrid compromise

46. Parallelism occurs when:
A. Single-thread processes on multicore
B. Multithreaded processes on single core
C. Multithreaded processes on multicore
D. All answers

47. Concurrency occurs when:
A. Multithreaded processes on single core
B. Single-thread processes on single core
C. Single-thread processes on multicore
D. Multithreaded processes on multicore

48. Thread communication vs process communication:
A. Slower because of libraries
B. Faster because no context switch
C. Faster because shared address space
D. No difference

49. Kernel thread:
A. Always maps to one user thread
B. Managed in user space
C. Smallest schedulable unit
D. OS internal process

50. Using a lock requires:
A. Lock initially free
B. Acquire before critical section
C. Release after critical section
D. All answers

51. Acquiring a lock:
A. Must be atomic
B. Requires hardware atomic instructions
C. Requires disabling interrupts
D. None

52. Semaphore can be used to:
A. Enforce scheduling
B. Access kernel code
C. Exchange messages between processes/threads
D. Manage interrupts

53. wait() on semaphore value 2:
A. Value stays 2 and continues
B. Value becomes 1 and blocks
C. Value becomes 3 and continues
D. Value becomes 1 and continues

54. test-and-set instruction:
A. Atomic instruction for synchronization
B. Disables interrupts
C. Updates multiple registers
D. Resets semaphore

55. Difference between deadlock and starvation:
A. User vs system code
B. Starvation blocks whole system
C. No difference
D. Deadlock blocks whole system

56. Producer-consumer execution result (val=73):
A. 72
B. 73
C. 74
D. Insufficient data

57. Producer-consumer execution result (val=24):
A. 23
B. 24
C. 25
D. Insufficient data

58. Producer-consumer execution result (val=19):
A. 18
B. 19
C. 20
D. Insufficient data

59. Resource Allocation Graph state:
A. Depends on scheduler
B. Deadlock present
C. No deadlock
D. Impossible to answer

60. Resource Allocation Graph state:
A. Depends on scheduler
B. Deadlock present
C. No deadlock
D. Impossible to answer

⸻

Deadlocks (continued)

61. Resource Allocation Graph state:
A. Certainly deadlock
B. Could have deadlock
C. Certainly no deadlock
D. Impossible to answer

62. Resource Allocation Graph state:
A. Certainly deadlock
B. Could have deadlock
C. Certainly no deadlock
D. Impossible to answer

⸻

Main Memory

63. Address binding means:
A. Translation from logical to physical addresses
B. Initialization of global variables
C. Linking code with libraries
D. None

64. Swapping allows to:
A. Implement dynamic relocation
B. Solve external fragmentation
C. Temporarily move processes to disk
D. Swap memory areas of processes

65. Paging:
A. Logical address space divided into fixed-size pages
B. Needs no hardware support
C. Physical address space divided into frames
D. Solves internal fragmentation

66. TLB:
A. Shared by all processes
B. Speeds address translation
C. Contains subset of page table
D. All answers

67. Page table size:
A. Directly proportional to page size
B. Adapts to memory requests
C. Depends on page size
D. Changes dynamically

68. Page table size:
A. Dynamic per process
B. Directly proportional to page size
C. Inversely proportional to page size
D. Adapts to memory requests

69. Logical address = 576, base = 24 → physical address?
A. 576
B. 552
C. 600
D. Insufficient data

70. Process = 2488 B, block = 2699 B:
A. Allocate full block → internal fragmentation
B. Allocate needed → external fragmentation
C. Wait for multiple block
D. Wait for smaller block

71. Process = 4996 B, block = 5016 B:
A. Wait smaller block
B. Allocate full block → internal fragmentation
C. Wait multiple block
D. Allocate needed → external fragmentation

72. Worst-fit, need 99 KiB (102, 99, 256, 128):
A. A
B. C
C. B
D. D

73. Worst-fit, need 99 KiB (300,600,350,200,750,125):
A. B
B. Cannot allocate
C. C + remaining to A
D. E

74. First-fit need 128 KiB (105,916,129,80):
A. A
B. D
C. B
D. C

75. First-fit need 115 KiB (300,600,350,200,750,125):
A. A
B. F
C. E
D. D

76. Best-fit need 375 KiB (300,600,350,200,750,125):
A. B
B. C + remaining to A
C. E
D. Cannot allocate

77. Best-fit need 34 KiB (36,90,42,35):
A. A
B. B
C. C
D. D

78. 4 KiB memory, 2 B words, page 128 B → bits?
A. p=6 offset=5
B. p=7 offset=5
C. p=5 offset=7
D. p=5 offset=6

79. Memory 512 B, frame 16 B, address 197:
A. p=5 off=12
B. Insufficient data
C. p=13 off=0
D. p=12 off=5

80. Memory 100 B, frame 10 B, address 37:
A. p=3 off=7
B. Insufficient data
C. p=7 off=3
D. p=0 off=37

81. Process 2097 B, block 2104 B:
A. Wait multiple
B. Allocate full block → internal fragmentation
C. Wait smaller
D. Allocate needed → external fragmentation

82. 2 KiB memory, word size 4 B → bits?
A. 2
B. 9
C. 11
D. Insufficient data

83. 4 KiB memory, word size 2 B → bits?
A. 10
B. 11
C. 12
D. Insufficient data

84. 8 KiB memory, byte addressing, page 128 B → entries?
A. Insufficient data
B. 13
C. 64
D. 7

85. 8 KiB memory, word size 4 B, page 256 B → entries?
A. 32
B. 2048
C. 8
D. 5

86. 16 KiB memory, word 4 B, page 64 B → entries?
A. 8
B. 14
C. 256
D. Insufficient data

87. Logical 21-bit, physical 16-bit, page 2 KiB → max physical memory?
A. 32 KiB
B. 64 KiB
C. 2 MiB
D. No limit

⸻

Virtual Memory

88. Virtual memory allows:
A. Increase I/O efficiency
B. Keep only some pages in RAM
C. Decrease multiprogramming
D. Execute directly from disk

89. Idempotent instruction page fault:
A. Process terminates
B. Cannot cause page fault
C. Not re-executed
D. Re-executed

90. External fragmentation:
A. Needs hardware
B. Only solved by reboot
C. Due to contiguous allocation
D. Causes interrupt

91. External fragmentation:
A. Only solved by reboot
B. Causes interrupt
C. Needs hardware
D. Due to contiguous allocation/deallocation

92. Working set:
A. Fixed per time slice
B. Large
C. Small
D. Fixed entire execution

93. LRU with timestamps:
A. Increment counter
B. Update timestamp
C. Set validity bit
D. None

94. Page faults (LRU):
A. 4
B. 5
C. 6
D. 7

95. Page faults (LRU):
A. 10
B. 7
C. 9
D. 6

96. Page faults (LRU):
A. 2
B. 4
C. 5
D. 1

97. Page faults (FIFO):
A. 6
B. 7
C. 4
D. 8

98. Page faults (FIFO):
A. 6
B. 7
C. 5
D. 4

99. Page faults (FIFO):
A. 7
B. 8
C. 6
D. 5

⸻

Secondary Memory

100. Contiguous file allocation:
A. Good random & sequential
B. Causes fragmentation
C. Needs free space tracking
D. All answers

101. Prefer contiguous allocation when disk is:
A. Read-only CD/DVD
B. Magnetic disk
C. SSD
D. None

102. Seek time:
A. Position head on sector
B. Includes transfer time
C. Position head on cylinder
D. Negligible

103. Disk 15 cylinders × 500 MB → capacity?
A. 7.5 GB
B. 75 GB
C. 750 MB
D. Insufficient data

104. Expected memory access time problem.
A. ~30.5 ns
B. ~30.5 µs
C. ~3.05 µs
D. ~305 ns

105. Expected memory access time problem.
A. ~150.025 µs
B. ~15.025 ns
C. ~150.025 ns
D. ~15.025 µs

106. Page fault probability problem.
A. ~0.02%
B. ~0.2%
C. ~0.002%
D. ~0.0002%

107. Page fault probability constraint.
A. Insufficient data
B. ~0.00024%
C. ~0.000024%
D. ~0.0000024%

108. Disk scheduling SSTF.
A. 86
B. 49
C. 123
D. 88

109. Disk scheduling FCFS.
A. 290
B. 240
C. 238
D. 265

110. Disk scheduling FCFS.
A. 595
B. 558
C. 650
D. 638

111. Disk scheduling FCFS.
A. 650
B. 522
C. 638
D. 595

112. Disk scheduling SCAN.
A. 76
B. 87
C. 46
D. 93

113. Disk scheduling C-SCAN.
A. 189
B. 193
C. 187
D. 196

114. Disk transfer data.
A. 9.375 MB
B. 7.5 MB
C. 937.5 KB
D. Insufficient data

115. Disk transfer data.
A. 9.375 MB
B. 70 MB
C. 70 KB
D. Insufficient data

116. Rotational delay.
A. 7 ms
B. 2 ms
C. 16 ms
D. Insufficient data

⸻

File Systems

117. Global open file table:
A. Shared by all processes
B. One entry per open file
C. Maintains counter
D. All answers

118. Local open file table:
A. Contains protection info
B. Contains disk location pointer
C. Contains pointer to global table
D. Shared among processes

119. Sequential file access example:
A. Compiler
B. DB search
C. Phone search
D. None

120. Indexed allocation preferred when file is:
A. Small
B. Large
C. Large sequential
D. Large random access

121. Linked allocation preferred when file is:
A. Small
B. Large random
C. Large
D. Large sequential

122. UNIX permissions 101000000:
A. Owner read+write
B. Owner read+execute
C. Owner write+execute
D. No permissions

123. UNIX permissions 011000000:
A. Owner read+write
B. Owner read+execute
C. No permissions
D. Owner write+execute

124. UNIX permissions 111101101:
A. Owner all, others write+execute
B. Owner all, others read+write
C. Owner all, others read+execute
D. Everyone all

125. ln file_1 file_2:
A. Hard link file_2→file_1
B. Hard link file_1→file_2
C. Soft link file_1→file_2
D. Soft link file_2→file_1

126. ln -s file_1 file_2:
A. Hard link file_2→file_1
B. Hard link file_1→file_2
C. Soft link file_1→file_2
D. Soft link file_2→file_1

127. Multi-level indexed file max size:
A. ~20.2 KB
B. ~20.2 MB
C. ~20.7 KB
D. ~20.7 MB



A process is essentially a running program, but by sharing the time of the process on each CPU, we can allow each process to have access of the whole CPU

Time sharing: allocating time for each process to run independently

Space sharing: partitioning total space across a disk is an example 

Scheduling policy: which program should the OS run on the CPU

Process machine state: memory (address space), registers (instructionds read/update these), program counter (which instruction we are on), stack pointer (which address has the top of the stack)
    stack will have parameters, local variables, and return addresses
also each process will have I/O, so which files it has open at the time 

Process API:
    - Create, destroy, wait, misc (suspend, etc.), status (how long it is running, its state)


Process creation steps:
    - We have our executable format, then a program is loaded from the disk (exe file) into memory, used to be done all at once (eagerly), now is done lazily (when needed)
    - The stack will then be initialized, argc and argv are added
    - heap is allocated from dynamically managed memory
    - in UNIX, three file descriptors are opened up, stdin, stdout, stderr
    - then it jumps to main to start running the code 

States of a process:
    - Running, on the processor, executing instructions
    - Ready, ready to run but OS hasn't run it
    - Blocked: process has done something which has made it unable to run, like requesting I/O from the disk, so while it does I/O other processes can run on the processor
    Life cycle: Ready --> (scheduled) Running --> (I/O) Blocked --> (I/O done) Ready --> (Scheduled) Running


Data Structures:
 Process list: for the processes that are ready, and some other info, but also needs to track blocked processes
 register context holds stopped processes, register contents
 when a process is stopped, its register contents are stored to that memory location (in register context), basically a struct with many integers about what is in each register

 each proc is a struct that has many fields, including PID, state, parent, memory start, size, kernel stack, etc.
 crucial to context switching

  Each structure that contains information about a process also known as a PCB (process control block, process descriptor)

  fork and wait() are importnat because of the UNIX shell, you can execute programs, pipe outputs all with these APIs

  piping output to another file is just a matter of changing the stdout to the file descriptor of that file in a child process


Chapter 5:
    Can send signals, sigint, sigstp
    admin is a super user, killing processes, etc. 
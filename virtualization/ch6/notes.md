Limited Direct Execution

6.1: direct execution means to just run the program on the CPU, so when OS wasnts to run something we create a process entry, allocate memory, load code from disk to memory, run the main function, return and then free memory, remove from process list

Fails to addres, how the OS can stop running unsafe things and also how to stop it and switch with time sharing 

User mode, code runs in user ode that restricts it, it can't issue I/O requests

when in kernel mode, the code runs privileged operations like I/O, restricted instructions 

When a user wants to get privileged information, like on a disk, we use system calls, which allows kernel to expose some functionality 
    - includes accesing file system, creating/destroying pocesses, allocating memory, communication with processes

Trap instruction: jumps into the kernel, raises privilege to kernel mode, and then in the kernel, the system can now perform what operations it needs 

When done, OS calls return-from-trap which goes back into the program and reduces privilege to user mode 

How is this done, in x86: processor pushes program counter, flga, and registers onto a process kernel stack, and pops them off when the execution is done

Kernel makes a trap table when it boots up, this tells the kernel what to do when in trap mode

OS makes note of all of these trap handlers with some instruction, and once the hardware is informed, it remembers location

System call number is used to assign each system call a specific ID, user code is responsible for placing the appropriate one there (validation of arguments is needed, so we don't write into malicious parts of memory (where kernel is))

LDE has two main steps:
- at boot time, kernel makes trap table, and CPU remebers its location for use later 
- 2nd, kernel sets up things before returning from a trap instruction to start execution, and then switches CPU to user mode to run process

Switching between processes:
If CPU runs a process, OS is technically not running 
How does OS take control of CPU

Cooperative: 
OS trusts process to behave, if it runs too long, it will periodically give up CPU to run another task 

Transfer is done via system calls, there is a yield call which transfers conrol to OS

On another note, if process does illegal things like division by zero, or accessing wrong parts of memory, it will generate a trap to the OS, so we get control back to the OS by system call of yield or some illegal operation 

Non-cooperative: 
- Time ineterrupt, timer device can be made to interrupt every couple milliseconds, OS gains control on interrupr, and can do what it pleases
- OS informs hardware of code to run when the timer interrupt happens, and also during boot sequence, OS starts the timer

Saving and restoring context:
It is important to save the context of programs when you make a system call
Scheduler will determine if we continue or change to a different process

If switch, OS will execute some piece of code which is a context switch: saving register values for process (onto kernel stack), save kernel stack pointer, program counter, and switch to kernel stack and restore a few for the other process, after this return, the other process takes over

In a switch from A to B, there are two types of save/srestores, oen for the user registers of the process (saved by hardware using kernel stack), when the OS decides to switch, kernel registers are saved by software into memory, moves the system from running as if it trapped into kernel from A like it trapped into from kernel B

The point of having a kernel stack for A, is to store the program counter, stack, poitnter all that stuff somewhere

OS will also save the kernel-mode state of A, so OS needs to remember information of A's instruction in the kernel so it can resume A's kernel path and return back to user A's code

Timer interrupt:
    hardware saves user registers onto current process's kernel stack

Context switch:
    OS saves kernel registers into current process's process structure
    OS loads another process's saved kernel registers
    CPU starts using the other process's kernel stack

Return from interrupt:
    hardware restores user registers from that process's kernel stack

Concurrency: 
- Not a worry, OS is concerned, but OS can disable interrupts during processing an inerrupt so it won't deliver twice to CPU
There are also locking scemes to prevent concurrent access to internal data structures

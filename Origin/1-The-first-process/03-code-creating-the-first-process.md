# Code: creating the first process

> 来源：book-rev7.pdf，第 17–24 页（英文原文）

After main initializes several devices and subsystems, it creates the first process by calling userinit (1239). Userinit ’s first action is to call allocproc. The job of allocproc (2205) is to allocate a slot (a struct proc) in the process table and to initialize the parts of the process’s state required for its kernel thread to execute. Allocproc is called for each new process, while userinit is called only for the very first process. Allocproc scans the proc table for a slot with state UNUSED (2211-2213). When it finds an unused slot, allocproc sets the state to EMBRYO to mark it as used and gives the process a unique pid (2201-2219). Next, it tries to allocate a kernel stack for the process’s kernel thread. If the memory allocation fails, allocproc changes the state back to UNUSED and returns zero to signal failure.

Now allocproc must set up the new process’s kernel stack. allocproc is written so that it can be used by fork as well as when creating the first process. allocproc sets up the new process with a specially prepared kernel stack and set of kernel registers that cause it to ‘‘return’’ to user space when it first runs. The layout of the prepared kernel stack will be as shown in Figure 1-3. allocproc does part of this work by setting up return program counter values that will cause the new process’s kernel thread to first execute in forkret and then in trapret (2236-2241). The kernel thread will start executing with register contents copied from p->context. Thus setting p- >context->eip to forkret will cause the kernel thread to execute at the start of forkret (2533). This function will return to whatever address is at the bottom of the stack. The context switch code (2708) sets the stack pointer to point just beyond the end of p->context. allocproc places p->context on the stack, and puts a pointer to trapret just above it; that is where forkret will return. trapret restores user registers from values stored at the top of the kernel stack and jumps into the process (3027). This setup is the same for ordinary fork and for creating the first process, though in the latter case the process will start executing at user-space location zero rather than at

![Figure 1-3](../../PIC/Figure-1-3.png)

Figure 1-3. A new kernel stack.

a return from fork.

As we will see in Chapter 3, the way that control transfers from user software to the kernel is via an interrupt mechanism, which is used by system calls, interrupts, and exceptions. Whenever control transfers into the kernel while a process is running, the hardware and xv6 trap entry code save user registers on the process’s kernel stack. userinit writes values at the top of the new stack that look just like those that would be there if the process had entered the kernel via an interrupt (2264-2270), so that the ordinary code for returning from the kernel back to the process’s user code will work. These values are a struct trapframe which stores the user registers. Now the new process’s kernel stack is completely prepared as shown in Figure 1-3.

The first process is going to execute a small program (initcode.S; (7700)). The process needs physical memory in which to store this program, the program needs to be copied to that memory, and the process needs a page table that refers to that memory.

userinit calls setupkvm (1737) to create a page table for the process with (at first) mappings only for memory that the kernel uses. We will study this function in detail in Chapter 2, but at a high level setupkvm and userinit create an address space as shown Figure 1-1.

The initial contents of the first process’s memory are the compiled form of initcode.S; as part of the kernel build process, the linker embeds that binary in the kernel and defines two special symbols, _binary_initcode_start and _binary_initcode_size, indicating the location and size of the binary. Userinit copies that binary into the new process’s memory by calling inituvm, which allocates one page of physical memory, maps virtual address zero to that memory, and copies the binary to that page (1803).

Then userinit sets up the trap frame (0602) with the initial user mode state: the %cs register contains a segment selector for the SEG_UCODE segment running at privilege level DPL_USER (i.e., user mode not kernel mode), and similarly %ds, %es, and %ss use SEG_UDATA with privilege DPL_USER. The %eflags FL_IF bit is set to allow hardware interrupts; we will reexamine this in Chapter 3.

The stack pointer %esp is set to the process’s largest valid virtual address, p->sz. The instruction pointer is set to the entry point for the initcode, address 0.

The function userinit sets p->name to initcode mainly for debugging. Setting p->cwd sets the process’s current working directory; we will examine namei in detail in Chapter 6.

Once the process is initialized, userinit marks it available for scheduling by setting p->state to RUNNABLE.

# Code: System calls

> 来源：book-rev7.pdf，第 33–44 页（英文原文）

For system calls, trap invokes syscall (3375). Syscall loads the system call number from the trap frame, which contains the saved %eax, and indexes into the system call tables. For the first system call, %eax contains the value SYS_exec (3207), and syscall will invoke the SYS_exec ’th entry of the system call table, which corresponds to invoking sys_exec.

Syscall records the return value of the system call function in %eax. When the trap returns to user space, it will load the values from cp->tf into the machine registers. Thus, when exec returns, it will return the value that the system call handler returned (3381). System calls conventionally return negative numbers to indicate errors, positive numbers for success. If the system call number is invalid, syscall prints an error and returns –1.

Later chapters will examine the implementation of particular system calls. This chapter is concerned with the mechanisms for system calls. There is one bit of mechanism left: finding the system call arguments. The helper functions argint and argptr, argstr retrieve the n ’th system call argument, as either an integer, pointer, or a string. argint uses the user-space %esp register to locate the n’th argument: %esp points at the return address for the system call stub. The arguments are right above it, at %esp+4. Then the nth argument is at %esp+4+4*n.

argint calls fetchint to read the value at that address from user memory and write it to *ip. fetchint can simply cast the address to a pointer, because the user and the kernel share the same page table, but the kernel must verify that the pointer by the user is indeed a pointer in the user part of the address space. The kernel has set up the page-table hardware to make sure that the process cannot access memory outside its local private memory: if a user program tries to read or write memory at an address of p->sz or above, the processor will cause a segmentation trap, and trap will kill the process, as we saw above. Now though, the kernel is running and it can derefence any address that the user might have passed, so it must check explicitly that the address is below p->sz

argptr is similar in purpose to argint: it interprets the n th system call argument. argptr calls argint to fetch the argument as an integer and then checks if the integer as a user pointer is indeed in the user part of the address space. Note that two checks occur during a call to code argptr. First, the user stack pointer is checked during the fetching of the argument. Then the argument, itself a user pointer, is checked.

argstr is the final member of the system call argument trio. It interprets the n th argument as a pointer. It ensures that the pointer points at a NUL-terminated string and that the complete string is located below the end of the user part of the address space.

The system call implementations (for example, sysproc.c and sysfile.c) are typically wrappers: they decode the arguments using argint, argptr, and argstr and then call the real implementations. In chapter 2, sys_exec uses these functions to get at its arguments.

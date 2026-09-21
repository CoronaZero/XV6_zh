# Code: The first system call

> 来源：book-rev7.pdf，第 33–44 页（英文原文）

Chapter 1 ended with initcode.S invoking a system call. Let’s look at that again (7713). The process pushed the arguments for an exec call on the process’s stack, and put the system call number in %eax. The system call numbers match the entries in the syscalls array, a table of function pointers (3350). We need to arrange that the int instruction switches the processor from user mode to kernel mode, that the kernel invokes the right kernel function (i.e., sys_exec), and that the kernel can retrieve the arguments for sys_exec. The next few subsections describes how xv6 arranges this for system calls, and then we will discover that we can reuse the same code for interrupts and exceptions.

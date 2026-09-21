# Code: Context switching

> 来源：book-rev7.pdf，第 53–66 页（英文原文）

As shown in Figure 5-1, to switch between processes, xv6 performs two kinds of

![Figure 5-1](../../PIC/Figure-5-1.png)

Figure 5-1. Switching from one user process to another. In this example, xv6 runs with one CPU (and

thus one scheduler thread). context switches at a low level: from a process’s kernel thread to the current CPU’s scheduler thread, and from the scheduler thread to a process’s kernel thread. xv6 never directly switches from one user-space process to another; this happens by way of a user-kernel transition (system call or interrupt), a context switch to the scheduler, a context switch to a new process’s kernel thread, and a trap return. In this section we’ll example the mechanics of switching between a kernel thread and a scheduler thread.

Every xv6 process has its own kernel stack and register set, as we saw in Chapter 2. Each CPU has a separate scheduler thread for use when it is executing the scheduler rather than any process’s kernel thread. Switching from one thread to another involves saving the old thread’s CPU registers, and restoring previously-saved registers of the new thread; the fact that %esp and %eip are saved and restored means that the CPU will switch stacks and switch what code it is executing.

swtch doesn’t directly know about threads; it just saves and restores register sets, called contexts. When it is time for the process to give up the CPU, the process’s kernel thread will call swtch to save its own context and return to the scheduler context. Each context is represented by a struct context*, a pointer to a structure stored on the kernel stack involved. Swtch takes two arguments: struct context **old and struct context *new. It pushes the current CPU register onto the stack and saves the stack pointer in *old. Then swtch copies new to %esp, pops previously saved registers, and returns.

Instead of following the scheduler into swtch, let’s instead follow our user process back in. We saw in Chapter 3 that one possibility at the end of each interrupt is that trap calls yield. Yield in turn calls sched, which calls swtch to save the current context in proc->context and switch to the scheduler context previously saved in cpu->scheduler (2516).

Swtch (2702) starts by loading its arguments off the stack into the registers %eax and %edx (2709-2710); swtch must do this before it changes the stack pointer and can no longer access the arguments via %esp. Then swtch pushes the register state, creating a context structure on the current stack. Only the callee-save registers need to be saved; the convention on the x86 is that these are %ebp, %ebx, %esi, %ebp, and %esp. Swtch pushes the first four explicitly (2713-2716); it saves the last implicitly as the struct context* written to *old (2719). There is one more important register: the program counter %eip was saved by the call instruction that invoked swtch and is on the stack just above %ebp. Having saved the old context, swtch is ready to restore the new one. It moves the pointer to the new context into the stack pointer (2720). The new stack has the same form as the old one that swtch just left—the new stack was the old one in a previous call to swtch —so swtch can invert the sequence to restore the new context. It pops the values for %edi, %esi, %ebx, and %ebp and then returns (2723-2727). Because swtch has changed the stack pointer, the values restored and the instruction address returned to are the ones from the new context.

In our example, sched called swtch to switch to cpu->scheduler, the per-CPU scheduler context. That context had been saved by scheduler ’s call to swtch (2478). When the swtch we have been tracing returns, it returns not to sched but to scheduler, and its stack pointer points at the current CPU’s scheduler stack, not initproc ’s kernel stack.

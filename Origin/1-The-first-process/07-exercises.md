# Exercises

> 来源：book-rev7.pdf，第 17–24 页（英文原文）

1. Set a breakpoint at swtch. Single step with gdb’s stepi through the ret to forkret, then use gdb’s finish to proceed to trapret, then stepi until you get to initcode at virtual address zero. 2. KERNBASE limits the amount of memory a single process can use, which might be irritating on a machine with a full 4 GB of RAM. Would raising KERNBASE allow a process to use more memory?

# User part of an address space

> 来源：book-rev7.pdf，第 25–32 页（英文原文）

Figure 2-3 shows the layout of the user memory of an executing process in xv6. The heap is above the stack so that it can expand (with sbrk). The stack is a single page, and is shown with the initial contents as created by exec. Strings containing the

![Figure 2-3](../../PIC/Figure-2-3.png)

Figure 2-3. Memory layout of a user process with its initial stack.

command-line arguments, as well as an array of pointers to them, are at the very top of the stack. Just under that are values that allow a program to start at main as if the function call main(argc, argv) had just started. To guard a stack growing off the stack page, xv6 places a guard page right below the stack. The guard page is not mapped and so if the stack runs off the stack page, the hardware will generate an exception because it cannot translate the faulting address.

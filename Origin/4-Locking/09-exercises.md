# Exercises

> 来源：book-rev7.pdf，第 45–52 页（英文原文）

1. get rid off the xchg in acquire. explain what happens when you run xv6? 2. move the acquire in iderw to before sleep. is there a race? why don’t you observe it when booting xv6 and run stressfs? increase critical section with a dummy loop; what do you see now? explain. 3. do posted homework question. 4. Setting a bit in a buffer’s flags is not an atomic operation: the processor makes a copy of flags in a register, edits the register, and writes it back. Thus it is important that two processes are not writing to flags at the same time. xv6 edits the B_BUSY bit only while holding buflock but edits the B_VALID and B_WRITE flags without holding any locks. Why is this safe?

# Memory ordering

> 来源：book-rev7.pdf，第 45–52 页（英文原文）

This chapter has assumed that processors start and complete instructions in the order in which they appear in the program. Many processors, however, execute instructions out of order to achieve higher performance. If an instruction takes many cycles to complete, a processor may want to issue the instruction early so that it can overlap with other instructions and avoid processor stalls. For example, a processor may notice that in a serial sequence of instruction A and B are not dependent on each other and start instruction B before A so that it will be completed when the processor completes A. Concurrency, however, may expose this reordering to software, which lead to incorrect behavior.

For example, one might wonder what happens if release just assigned 0 to lk- >locked, instead of using xchg. The answer to this question is unclear, because different generations of x86 processors make different guarantees about memory ordering. If lk->locked=0, were allowed to be re-ordered say after popcli, than acquire might break, because to another thread interrupts would be enabled before a lock is released. To avoid relying on unclear processor specifications about memory ordering, xv6 takes no risk and uses xchg, which processors must guarantee not to reorder.

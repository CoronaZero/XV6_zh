# Code: Locks

> 来源：book-rev7.pdf，第 45–52 页（英文原文）

Xv6’s represents a lock as a struct spinlock (1401). The critical field in the structure is locked, a word that is zero when the lock is available and non-zero when it is held. Logically, xv6 should acquire a lock by executing code like

```c
21 void
22 acquire(struct spinlock *lk)
23 {
24 for(;;) {
25 if(!lk->locked) {
26 lk->locked = 1;
27 break;
28 }
29 }
30 }
```

Unfortunately, this implementation does not guarantee mutual exclusion on a modern multiprocessor. It could happen that two (or more) CPUs simultaneously reach line 25, see that lk->locked is zero, and then both grab the lock by executing lines 26 and 27. At this point, two different CPUs hold the lock, which violates the mutual exclusion property. Rather than helping us avoid race conditions, this implementation of acquire has its own race condition. The problem here is that lines 25 and 26 executed as separate actions. In order for the routine above to be correct, lines 25 and 26 must execute in one atomic (i.e., indivisible) step.

To execute those two lines atomically, xv6 relies on a special 386 hardware instruction, xchg (0569). In one atomic operation, xchg swaps a word in memory with the contents of a register. The function acquire (1474) repeats this xchg instruction in a loop; each iteration reads lk->locked and atomically sets it to 1 (1483). If the lock is held, lk->locked will already be 1, so the xchg returns 1 and the loop continues. If the xchg returns 0, however, acquire has successfully acquired the lock— locked was 0 and is now 1—so the loop can stop. Once the lock is acquired, acquire records, for debugging, the CPU and stack trace that acquired the lock. When a process acquires a lock and forget to release it, this information can help to identify the culprit. These debugging fields are protected by the lock and must only be edited while holding the lock.

The function release (1502) is the opposite of acquire: it clears the debugging fields and then releases the lock.

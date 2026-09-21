# Real world

> 来源：book-rev7.pdf，第 45–52 页（英文原文）

Concurrency primitives and parallel programming are active areas of of research, because programming with locks is still challenging. It is best to use locks as the base for higher-level constructs like synchronized queues, although xv6 does not do this. If you program with locks, it is wise to use a tool that attempts to identify race conditions, because it is easy to miss an invariant that requires a lock.

User-level programs need locks too, but in xv6 applications have one thread of execution and processes don’t share memory, and so there is no need for locks in xv6 applications.

It is possible to implement locks without atomic instructions, but it is expensive, and most operating systems use atomic instructions.

Atomic instructions are not free either when a lock is contented. If one processor has a lock cached in its local cache, and another processor must acquire the lock, then the atomic instruction to update the line that holds the lock must move the line from the one processor’s cache to the other processor’s cache, and perhaps invalidate any other copies of the cache line. Fetching a cache line from another processor’s cache can be orders of magnitude more expensive than fetching a line from a local cache.

To avoid the expenses associated with locks, many operating systems use lock-free data structures and algorithms, and try to avoid atomic operations in those algorithms. For example, it is possible to implemented a link list like the one in the beginning of the chapter that requires no locks during list searches, and one atomic instruction to insert an item in a list.

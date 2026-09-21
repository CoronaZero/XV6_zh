# Buffer cache Layer

> 来源：book-rev7.pdf，第 67–81 页（英文原文）

The buffer cache has two jobs: (1) synchronize access to disk blocks to ensure that only one copy of a block is in memory and that only one kernel thread at a time uses that copy; (2) cache popular blocks so that they don’t to be re-read from the slow disk. The code is in bio.c.

The main interface exported by the buffer cache consists of bread and bwrite; the former obtains a buffer containing a copy of a block which can be read or modified in memory, and the latter writes a modified buffer to the appropriate block on the disk. A kernel thread must release a buffer by calling brelse when it is done with it.

The buffer cache synchronizes access to each block by allowing at most one kernel thread to have a reference to the block’s buffer. If one kernel thread has obtained a reference to a buffer but hasn’t yet released it, other threads’ calls to bread for the same block will wait. Higher file system layers rely on the buffer cache’s block sychronization to help them maintain invariants.

The buffer cache has a fixed number of buffers to hold disk blocks, which means that if the file system asks for a block that is not already in the cache, the buffer cache must recycle a buffer currently holding some other block. The buffer cache recycles the least recently used buffer for the new block. The assumption is that the least recently used buffer is the one least likely to be used again soon.

![Figure 6-2](../../PIC/Figure-6-2.png)

Figure 6-2. Structure of the xv6 file system. The header fs.h (3650) contains constants and data struc-

tures describing the exact layout of the file system.

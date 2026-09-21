# Log design

> 来源：book-rev7.pdf，第 67–81 页（英文原文）

The log resides at a known fixed location at the end of the disk. It consists of a header block followed by a sequence of data blocks. The header block contains an array of sector numbers, one for each of the logged data blocks. The header block also contains the count of logged blocks. Xv6 writes the header block when a transaction commits, but not before, and sets the count to zero after copying the logged blocks to the file system. Thus a crash midway through a transaction will result in a count of zero in the log’s header block; a crash after a commit will result in a non-zero count.

Each system call’s code indicates the start and end of the sequence of writes that must be atomic; we’ll call such a sequence a transaction, though it is much simpler than a database transaction. Only one system call can be in a transaction at any one time: other processes must wait until any ongoing transaction has finished. Thus the log holds at most one transaction at a time.

Xv6 does not allow concurrent transactions, in order to avoid the following kind of problem. Suppose transaction X has written a modification to an inode into the log. Concurrent transaction Y then reads a different inode in the same block, updates that inode, writes the inode block to the log, and commits. This is a disaster: the inode block that Y’s commit writes to the disk contains modifications by X, which has not committed. A crash and recovery at this point would expose one of X’s modifications but not all, thus breaking the promise that transactions are atomic. There are sophisticated ways to solve this problem; xv6 solves it by outlawing concurrent transactions.

Xv6 allows read-only system calls to execute concurrently with a transaction. Inode locks cause the transaction to appear atomic to the read-only system call.

Xv6 dedicates a fixed amount of space on the disk to hold the log. No system call can be allowed to write more distinct blocks than there is space in the log. This is not a problem for most system calls, but two of them can potentially write many blocks: write and unlink. A large file write may write many data blocks and many bitmap blocks as well as an inode block; unlinking a large file might write many bitmap blocks and an inode. Xv6’s write system call breaks up large writes into multiple smaller writes that fit in the log, and unlink doesn’t cause problems because in practice the xv6 file system uses only one bitmap block.

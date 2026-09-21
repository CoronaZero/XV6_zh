# Code: logging

> 来源：book-rev7.pdf，第 67–81 页（英文原文）

A typical use of the log in a system call looks like this:

```c
begin_trans();
...
bp = bread(...);
bp->data[...] = ...;
log_write(bp);
...
commit_trans();
```

begin_trans (4277) waits until it obtains exclusive use of the log and then returns. log_write (4325) acts as a proxy for bwrite; it appends the block’s new content to the log on disk and records the block’s sector number in memory. log_write leaves the modified block in the in-memory buffer cache, so that subsequent reads of the block during the transaction will yield the modified block. log_write notices when a block is written multiple times during a single transaction, and overwrites the block’s previous copy in the log. commit_trans (4301) first writes the log’s header block to disk, so that a crash after this point will cause recovery to re-write the blocks in the log. commit_trans then calls install_trans (4221) to read each block from the log and write it to the proper place in the file system. Finally commit_trans writes the log header with a count of zero, so that a crash after the next transaction starts will result in the recovery code ignoring the log. recover_from_log (4268) is called from initlog (4205), which is called during boot before the first user process runs. (2544) It reads the log header, and mimics the actions of commit_trans if the header indicates that the log contains a committed transaction. An example use of the log occurs in filewrite (5352). The transaction looks like this:

```c
begin_trans();
ilock(f->ip);
r = writei(f->ip, ...);
iunlock(f->ip);
commit_trans();
```

This code is wrapped in a loop that breaks up large writes into individual transactions of just a few sectors at a time, to avoid overflowing the log. The call to writei writes many blocks as part of this transaction: the file’s inode, one or more bitmap blocks, and some data blocks. The call to ilock occurs after the begin_trans as part of an overall strategy to avoid deadlock: since there is effectively a lock around each transaction, the deadlock-avoiding lock ordering rule is transaction before inode.

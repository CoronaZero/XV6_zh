# Overview

> 来源：book-rev7.pdf，第 67–81 页（英文原文）

The xv6 file system implementation is organized in 6 layers, as shown in Figure 6-1. The lowest layer reads and writes blocks on the IDE disk through the buffer cache, which synchronizes access to disk blocks, making sure that only one kernel process at a time can edit the file system data stored in any particular block. The second layer allows higher layers to wrap updates to several blocks in a transaction, to ensure that the blocks are updated atomically (i.e., all of them are updated or none). The third layer provides unnamed files, each represented using an inode and a sequence of blocks holding the file’s data. The fourth layer implements directories as a special kind of inode whose content is a sequence of directory entries, each of which contains a name and a reference to the named file’s inode. The fifth layer provides hierarchical path names like /usr/rtm/xv6/fs.c, using recursive lookup. The final layer abstracts many Unix resources (e.g., pipes, devices, files, etc.) using the file system interface, simplifying the lives of application programmers.

The file system must have a plan for where it stores inodes and content blocks on the disk. To do so, xv6 divides the disk into several sections, as shown in Figure 6-2. The file system does not use block 0 (it holds the boot sector). Block 1 is called the superblock; it contains metadata about the file system (the file system size in blocks,

![Figure 6-1](../../PIC/Figure-6-1.png)

Figure 6-1. Layers of the xv6 file system.

the number of data blocks, the number of inodes, and the number of blocks in the log). Blocks starting at 2 hold inodes, with multiple inodes per block. After those come bitmap blocks tracking which data blocks in use. Most of the remaining blocks are data blocks, which hold file and directory contents. The blocks at the end of the disk hold a log that is part of the transaction layer.

The rest of this chapter discusses each layer, starting from the bottom. Look out for situations where well-chosen abstractions at lower layers ease the design of higher ones.

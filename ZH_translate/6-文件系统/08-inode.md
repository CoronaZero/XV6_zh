# Inode

> 来源：book-rev7.pdf，第 67–81 页（中文翻译）

术语 inode 可以有两种相关含义。它可以指磁盘上的数据结构，包含文件的大小和数据块号列表。或者“inode”可以指内存中的 inode，它包含磁盘 inode 的副本以及内核中需要的额外信息。

所有磁盘上的 inode 都打包在磁盘上一个称为 inode 块的连续区域中。每个 inode 的大小相同，因此给定数字 n，很容易找到磁盘上第 n 个 inode。事实上，这个数字 n 称为 inode 号或 i-number，是实现中识别 inode 的方式。

磁盘 inode 由 struct dinode (3676) 定义。type 字段区分文件、目录和特殊文件（设备）。类型为零表示磁盘上的 inode 空闲。nlink 字段计算引用此 inode 的目录项数量，以便识别 inode 何时应被释放。size 字段记录文件中内容的字节数。addrs 数组记录保存文件内容的磁盘块的块号。

内核在内存中保存活动 inode 的集合；struct inode (3762) 是磁盘上 struct dinode 的内存副本。只有存在引用该 inode 的 C 指针时，内核才在内存中存储 inode。ref 字段是引用计数，记录指向内存 inode 的 C 指针数量，如果引用计数降到零，内核将丢弃该 inode。iget 和 iput 函数获取和释放对 inode 的指针，修改引用计数。指向 inode 的指针可以来自文件描述符、当前工作目录以及 exec 等瞬时内核代码。

持有 iget() 返回的 inode 指针保证该 inode 将留在缓存中，不会被删除（尤其不会被复用为不同的文件）。因此 iget() 返回的指针是一种弱锁，尽管它不允许持有者实际查看 inode。文件系统代码的许多部分都依赖 iget() 的这一行为，既用于持有对 inode 的长期引用（如打开文件和当前目录），也用于在操作多个 inode 的代码（如路径名查找）中同时避免死锁和防止竞态。

iget 返回的 struct inode 可能没有任何有用内容。为了确保它保存磁盘 inode 的副本，代码必须调用 ilock。ilock 锁定 inode（使其他进程无法 ilock 它），并且如果尚未读取，则从磁盘读取 inode。iunlock 释放 inode 上的锁。将获取 inode 指针与锁定分开，有助于在某些情况下避免死锁，例如在目录查找期间。多个进程可以持有 iget 返回的 inode 的 C 指针，但同一时刻只能有一个进程锁定该 inode。

inode 缓存只缓存内核代码或数据结构持有 C 指针的 inode。它的主要工作实际上是同步多个进程的访问，而不是缓存。如果一个 inode 被频繁使用，缓冲区缓存可能会在内存中保留它，即使它没有被 inode 缓存保留。
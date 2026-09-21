# Introduction

> 来源：book-rev7.pdf，第 67–81 页（英文原文）

The purpose of a file system is to organize and store data. File systems typically support sharing of data among users and applications, as well as persistence so that data is still available after a reboot.

The xv6 file system provides Unix-like files, directories, and pathnames (see Chapter 0), and stores its data on an IDE disk for persistence (see Chapter 3). The file system addresses several challenges: • The file system needs on-disk data structures to represent the tree of named di-

rectories and files, to record the identities of the blocks that hold each file’s con-

tent, and to record which areas of the disk are free. • The file system must support crash recovery. That is, if a crash (e.g., power

failure) occurs, the file system must still work correctly after a restart. The risk is

that a crash might interrupt a sequence of updates and leave inconsistent on-disk

data structures (e.g., a block that is both used in a file and marked free). • Different processes may operate on the file system at the same time, and must co-

ordinate to maintain invariants. • Accessing a disk is orders of magnitude slower than accessing memory, so the file

system must maintain an in-memory cache of popular blocks. The rest of this chapter explains how xv6 addresses these challenges.

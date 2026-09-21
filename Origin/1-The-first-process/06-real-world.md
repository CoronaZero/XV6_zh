# Real world

> 来源：book-rev7.pdf，第 17–24 页（英文原文）

Most operating systems have adopted the process concept, and most processes look similar to xv6’s. A real operating system would find free proc structures with an explicit free list in constant time instead of the linear-time search in allocproc; xv6 uses the linear scan (the first of many) for simplicity. xv6’s address space layout has the defect that it cannot make use of more than 2 GB of physical RAM. It’s possible to fix this, though the best plan would be to switch to a machine with 64-bit addresses.

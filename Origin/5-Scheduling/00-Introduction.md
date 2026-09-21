# Introduction

> 来源：book-rev7.pdf，第 53–66 页（英文原文）

Any operating system is likely to run with more processes than the computer has processors, and so some plan is needed to time share the processors between the processes. An ideal plan is transparent to user processes. A common approach is to provide each process with the illusion that it has its own virtual processor, and have the operating system multiplex multiple virtual processors on a single physical processor. This chapter how xv6 multiplexes a processor among several processes.

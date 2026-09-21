# Introduction

> 来源：book-rev7.pdf，第 17–24 页（英文原文）

This chapter explains what happens when xv6 first starts running, through the creation of the first process. In doing so, the text provides a glimpse of the implementation of all major abstractions that xv6 provides, and how they interact. Most of xv6 avoids special-casing the first process, and instead reuses code that xv6 must provide for standard operation. Subsequent chapters will explore each abstraction in more detail. Xv6 runs on Intel 80386 or later (‘‘x86’’) processors on a PC platform, and much of its low-level functionality (for example, its process implementation) is x86-specific. This book assumes the reader has done a bit of machine-level programming on some architecture, and will introduce x86-specific ideas as they come up. Appendix A briefly outlines the PC platform.

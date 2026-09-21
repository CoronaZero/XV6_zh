# Introduction

> 来源：book-rev7.pdf，第 87–91 页（英文原文）

x GB CPU Selector Offset Translation Segment Address Linear Translation Page Physical Address Logical Address 0 RAM 

![Figure B-1](../../PIC/Figure-B-1.png)

Figure B-1. The relationship between logical, linear, and physical addresses.

**Appendix B**

**The boot loader**

When an x86 PC boots, it starts executing a program called the BIOS, which is stored in non-volatile memory on the motherboard. The BIOS’s job is to prepare the hardware and then transfer control to the operating system. Specifically, it transfers control to code loaded from the boot sector, the first 512-byte sector of the boot disk. The boot sector contains the boot loader: instructions that load the kernel into memory. The BIOS loads the boot sector at memory address 0x7c00 and then jumps (sets the processor’s %ip) to that address. When the boot loader begins executing, the processor is simulating an Intel 8088, and the loader’s job is to put the processor in a more modern operating mode, to load the xv6 kernel from disk into memory, and then to transfer control to the kernel. The xv6 boot loader comprises two source files, one written in a combination of 16-bit and 32-bit x86 assembly (bootasm.S; (8400)) and one written in C (bootmain.c; (8500)).

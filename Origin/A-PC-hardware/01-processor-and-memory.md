# Processor and memory

> 来源：book-rev7.pdf，第 83–85 页（英文原文）

A computer’s CPU (central processing unit, or processor) runs a conceptually simple loop: it consults an address in a register called the program counter, reads a machine instruction from that address in memory, advances the program counter past the instruction, and executes the instruction. Repeat. If the execution of the instruction does not modify the program counter, this loop will interpret the memory pointed at by the program counter as a sequence of machine instructions to run one after the other. Instructions that do change the program counter include branches and function calls.

The execution engine is useless without the ability to store and modify program data. The fastest storage for data is provided by the processor’s register set. A register is a storage cell inside the processor itself, capable of holding a machine word-sized value (typically 16, 32, or 64 bits). Data stored in registers can typically be read or written quickly, in a single CPU cycle.

PCs have a processor that implements the x86 instruction set, which was originally defined by Intel and has become a standard. Several manufacturers produce processors that implement the instruction set. Like all other PC standards, this standard is also evolving but newer standards are backwards compatible with past standards. The boot loader has to deal with some of this evolution because every PC processor starts simulating an Intel 8088, the CPU chip in the original IBM PC released in 1981. However, for most of xv6 you will be concerned with the modern x86 instruction set.

The modern x86 provides eight general purpose 32-bit registers— %eax, %ebx, %ecx, %edx, %edi, %esi, %ebp, and %esp —and a program counter %eip (the ‘ instruction pointer). The common e prefix stands for extended, as these are 32-bit extensions of the 16-bit registers %ax, %bx, %cx, %dx, %di, %si, %bp, %sp, and %ip. The two register sets are aliased so that, for example, %ax is the bottom half of %eax: writing to %ax changes the value stored in %eax and vice versa. The first four registers also have names for the bottom two 8-bit bytes: %al and %ah denote the low and high 8 bits of %ax; %bl, %bh, %cl, %ch, %dl, and %dh continue the pattern. In addition to these registers, the x86 has eight 80-bit floating-point registers as well as a handful of special-purpose registers like the control registers %cr0, %cr2, %cr3, and %cr4; the debug registers %dr0, %dr1, %dr2, and %dr3; the segment registers %cs, %ds, %es, %fs, %gs, and %ss; and the global and local descriptor table pseudo-registers %gdtr and %ldtr. The control registers and segment registers are important to any operating system. The floating-point and debug registers are less interesting and not used by xv6.

Registers are fast but expensive. Most processors provide at most a few tens of general-purpose registers. The next conceptual level of storage is the main random-access memory (RAM). Main memory is 10-100x slower than a register, but it is much cheaper, so there can be more of it. One reason main memory is relatively slow is that it is physically separate from the processor chip. An x86 processor has a few dozen registers, but a typical PC today has gigabytes of main memory. Because of the enormous differences in both access speed and size between registers and main memory, most processors, including the x86, store copies of recently-accessed sections of main memory in on-chip cache memory. The cache memory serves as a middle ground between registers and memory both in access time and in size. Today’s x86 processors typically have two levels of cache, a small first-level cache with access times relatively close to the processor’s clock rate and a larger second-level cache with access times in between the first-level cache and main memory. This table shows actual numbers for an Intel Core 2 Duo system:

**Intel Core 2 Duo E7200 at 2.53 GHz**

TODO: Plug in non-made-up numbers!

**storage access time size**

register 0.6 ns 64 bytes

L1 cache 0.5 ns 64 kilobytes

L2 cache 10 ns 4 megabytes

main memory 100 ns 4 gigabytes

For the most part, x86 processors hide the cache from the operating system, so we can think of the processor as having just two kinds of storage—registers and memory—and not worry about the distinctions between the different levels of the memory hierarchy.

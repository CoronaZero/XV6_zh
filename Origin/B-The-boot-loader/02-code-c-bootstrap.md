# Code: C bootstrap

> 来源：book-rev7.pdf，第 87–91 页（英文原文）

The C part of the boot loader, bootmain.c (8500), expects to find a copy of the kernel executable on the disk starting at the second sector. The kernel is an ELF format binary, as we have seen in Chapter 2. To get access to the ELF headers, bootmain loads the first 4096 bytes of the ELF binary (8514). It places the in-memory copy at address 0x10000.

The next step is a quick check that this probably is an ELF binary, and not an uninitialized disk. Bootmain reads the section’s content starting from the disk location off bytes after the start of the ELF header, and writes to memory starting at address paddr. Bootmain calls readseg to load data from disk (8538) and calls stosb to zero the remainder of the segment (8540). Stosb (0492) uses the x86 instruction rep stosb to initialize every byte of a block of memory.

The kernel has been compiled and linked so that it expects to find itself at virtual addresses starting at 0x80100000. That is, function call instructions mention destination addresses that look like 0xf01xxxxx; you can see examples in kernel.asm. This address is configured in kernel.ld. 0x80100000 is a relatively high address, towards the end of the 32-bit address space; Chapter 2 explains the reasons for this choice. There may not be any physical memory at such a high address. Once the kernel starts executing, it will set up the paging hardware to map virtual addresses starting at 0x80100000 to physical addresses starting at 0x00100000; the kernel assumes that there is physical memory at this lower address. At this point in the boot process, however, paging is not enabled. Instead, kernel.ld specifies that the ELF paddr start at 0x00100000, which causes the boot loader to copy the kernel to the low physical addresses to which the paging hardware will eventually point.

The boot loader’s final step is to call the kernel’s entry point, which is the instruction at which the kernel expects to start executing. For xv6 the entry address is

```c
0x10000c:
# objdump -f kernel
kernel: file format elf32-i386
architecture: i386, flags 0x00000112:
EXEC_P, HAS_SYMS, D_PAGED
start address 0x0010000c
```

By convention, the _start symbol specifies the ELF entry point, which is defined in the file entry.S (1036). Since xv6 hasn’t set up virtual memory yet, xv6’s entry point is the physical address of entry (1040).

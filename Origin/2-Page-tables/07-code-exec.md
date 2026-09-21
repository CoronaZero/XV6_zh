# Code: exec

> 来源：book-rev7.pdf，第 25–32 页（英文原文）

Exec is the system call that creates the user part of an address space. It initializes the user part of an address space from a file stored in the file system. Exec (5910) opens the named binary path using namei (5920), which is explained in Chapter 6. Then, it reads the ELF header. Xv6 applications are described in the widely-used ELF format, defined in elf.h. An ELF binary consists of an ELF header, struct elfhdr (0955), followed by a sequence of program section headers, struct proghdr (0974). Each proghdr describes a section of the application that must be loaded into memory; xv6 programs have only one program section header, but other systems might have separate sections for instructions and data.

The first step is a quick check that the file probably contains an ELF binary. An ELF binary starts with the four-byte ‘‘magic number’’ 0x7F, ’E’, ’L’, ’F’, or ELF_MAGIC (0952). If the ELF header has the right magic number, exec assumes that the binary is well-formed.

Exec allocates a new page table with no user mappings with setupkvm (5931), allocates memory for each ELF segment with allocuvm (5943), and loads each segment into memory with loaduvm (5945). allocuvm checks that the virtual addresses requested is below KERNBASE. loaduvm (1818) uses walkpgdir to find the physical address of the allocated memory at which to write each page of the ELF segment, and readi to read from the file.

The program section header for /init, the first user program created with exec, looks like this:

```c
# objdump -p _init
_init: file format elf32-i386
Program Header:
LOAD off 0x00000054 vaddr 0x00000000 paddr 0x00000000 align 2**2
filesz 0x000008c0 memsz 0x000008cc flags rwx
```

The program section header’s filesz may be less than the memsz, indicating that the gap between them should be filled with zeroes (for C global variables) rather than read from the file. For /init, filesz is 2240 bytes and memsz is 2252 bytes, and thus allocuvm allocates enough physical memory to hold 2252 bytes, but reads only 2240 bytes from the file /init.

Now exec allocates and initializes the user stack. It allocates just one stack page. Exec copies the argument strings to the top of the stack one at a time, recording the pointers to them in ustack. It places a null pointer at the end of what will be the argv list passed to main. The first three entries in ustack are the fake return PC, argc, and argv pointer.

Exec places an inaccessible page just below the stack page, so that programs that try to use more than one page will fault. This inaccessible page also allows exec to deal with arguments that are too large; in that situation, the copyout function that exec uses to copy arguments to the stack will notice that the destination page in not accessible, and will return –1.

During the preparation of the new memory image, if exec detects an error like an invalid program segment, it jumps to the label bad, frees the new image, and returns –1. Exec must wait to free the old image until it is sure that the system call will succeed: if the old image is gone, the system call cannot return –1 to it. The only error cases in exec happen during the creation of the image. Once the image is complete, exec can install the new image (5989) and free the old one (5990). Finally, exec returns 0.

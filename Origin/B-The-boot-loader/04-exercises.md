# Exercises

> 来源：book-rev7.pdf，第 87–91 页（英文原文）

1. Due to sector granularity, the call to readseg in the text is equivalent to readseg((uchar*)0x100000, 0xb500, 0x1000). In practice, this sloppy behavior turns out not to be a problem Why doesn’t the sloppy readsect cause problems? 2. something about BIOS lasting longer + security problems 3. Suppose you wanted bootmain() to load the kernel at 0x200000 instead of 0x100000, and you did so by modifying bootmain() to add 0x100000 to the va of each ELF section. Something would go wrong. What? 4. It seems potentially dangerous for the boot loader to copy the ELF header to memory at the arbitrary location 0x10000. Why doesn’t it call malloc to obtain the memory it needs?

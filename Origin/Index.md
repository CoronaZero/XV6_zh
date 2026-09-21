# Index

> 来源：book-rev7.pdf，第 93–95 页（英文原文）

Index

., 77, 79 convoys, 64 global descriptor table, 89

.., 77, 79 copyout, 31 I/O ports, 85

/init, 23, 31 coroutines, 56 I_BUSY, 75

_binary_initcode_size, 21 cp->killed, 38 I_VALID, 75

_binary_initcode_start, 21 cp->tf, 38 ialloc, 74–75, 79

_start, 90 cpu->scheduler, 22, 54–55 IDE_BSY, 42

acquire, 47–48, 50 CR0_PE, 89 IDE_DRDY, 42

addl, 22 CR0_PG, 20 IDE_IRQ, 41

address space, 17 CR_PSE, 32 ideinit, 41–42

allocproc, 20 crash recovery, 67 ideintr, 42, 50

allocuvm, 23, 30–31 create, 79 idelock, 49–50

alltraps, 36–37 current directory, 14 iderw, 42, 48, 50–51, 69–70

argc, 31 deadlocked, 59 idestart, 42

argint, 39 direct blocks, 75 idewait, 42

argptr, 39 dirlink, 77 idt, 36

argstr, 39 dirlookup, 77–79 idtinit, 40

argv, 31 DIRSIZ, 77 IF, 40

atomic, 47 DPL_USER, 22, 36 iget, 74–75, 77

B_BUSY, 41, 69–70 driver, 41 ilock, 73–75, 78

B_DIRTY, 41–43, 69–70 dup, 78 inb, 40

B_VALID, 41–43, 69 ELF format, 30 indirect block, 75

balloc, 73–74 ELF_MAGIC, 30 initcode, 23

bcache.head, 69 EMBRYO, 20 initcode.S, 21, 23, 35

begin_trans, 72–73 entry, 19, 90 initlog, 72

bfree, 73 entrypgdir, 19 initproc, 23

bget, 69 exception, 33 inituvm, 21

binit, 69 exec, 9–11, 23, 31, 36 inode, 15, 67, 73

block, 41 exit, 9, 23, 55–56, 63 insl, 42

bmap, 76 fetchint, 39 install_trans, 72

boot loader, 19, 87–89 file descriptor, 10 instruction pointer, 84

bootmain, 89 filealloc, 78 int, 34–36

bread, 68, 70 fileclose, 78 interface design, 7

brelse, 68, 70 filedup, 78 interrupt, 33

BSIZE, 75 fileread, 78, 81 interrupt handler, 34

buf_table_lock, 69 filestat, 78 ioapicenable, 41

buffer, 41, 68 filewrite, 73, 78, 81 iput, 74–75

busy waiting, 42 FL, 36 iret, 22, 35, 38

bwrite, 68, 70, 72 FL_IF, 22 IRQ_TIMER,, 40

chan, 58, 61 fork, 9–11, 78 itrunc, 75–76

child process, 9 forkret, 20, 22, 56 iunlock, 75

cli, 40, 50 freerange, 29 iupdate, 75

commit, 71 fsck, 80 kalloc, 29

commit_trans, 72 ftable, 78 KERNBASE, 19

conditional synchronization, 57 gdt, 88–89 kernel, 7

contexts, 54 gdtdesc, 89 kernel mode, 34

control registers, 84 getcmd, 10 kernel space, 7

kfree, 29 picenable, 41 skipelem, 78

kinit1, 29 pid, 9, 20 sleep, 50, 55, 58–60, 69

kinit2, 29 pipe, 13 sleep., 59

kmap, 28 piperead, 61 SLEEPING, 60–61

kvmalloc, 26, 28 pipewrite, 61 stat, 77–78

lapicinit, 40 polling, 42 stati, 77–78

linear address, 87–88 popal, 22 sti, 40, 50

links, 15 popcli, 50 stosb, 90

loaduvm, 31 popl, 22 struct buf, 41

lock, 45 printf, 9 struct context, 54

log, 71 priority inversion, 64 struct dinode, 74–75

log_write, 72 process, 7–8 struct dirent, 77

logical address, 87–88 program counter, 83 struct elfhdr, 30

main, 20, 22, 28–29, 36, 41, 69 programmable interrupt struct file, 78

malloc, 10 controler (PIC), 40 struct inode, 74

mappages, 28 protected mode, 88–89 struct pipe, 62

memory-mapped I/O, 85 ptable, 50 struct proc, 17, 62

mkdev, 79 ptable.lock, 55–56, 60–62 struct run, 29

mkdir, 79 PTE_P, 25 struct spinlock, 47

mpmain, 22 PTE_U, 23, 26–28 struct trapframe, 21

multiplex, 53 PTE_W, 25 superblock, 67

namei, 22, 30, 79 pushcli, 50 switchuvm, 22, 36, 40, 56

nameiparent, 77–79 race condition, 46 swtch, 22, 54–56, 63

namex, 77–78 read, 78 sys_exec, 36

NBUF, 69 readi, 31, 76–77 SYS_exec, 23, 38

NDIRECT, 75–76 readsb, 73 sys_link, 79

NINDIRECT, 75–76 readseg, 90 sys_mkdir, 79

O_CREATE, 79 real mode, 87 sys_mknod, 79

open, 78–79 recover_from_log, 72 sys_open, 79

outb, 40 recursive locks, 48 sys_pipe, 80

p->context, 20, 22, 56 release, 48, 50–51 sys_sleep, 50

p->cwd, 22 ret, 22 sys_unlink, 79

p->kstack, 18, 63 root, 14 syscall, 38

p->name, 22 round robin, 64 system calls, 7

p->parent, 62 RUNNABLE, 22, 56, 60–62 T_DEV, 77

p->pgdir, 18, 63 sbrk, 10, 29 T_DIR, 77

p->state, 18 sched, 54–56, 60, 63 T_FILE, 79

p->sz, 39 scheduler, 22, 55–56 T_SYSCALL, 23, 36, 38

p->xxx, 17 sector, 41 tf->trapno, 38

page, 25 SEG_KCPU, 37 thread, 18

page directory, 25 SEG_KDATA, 89 thundering herd, 64

page table entries (PTEs), 25 SEG_TSS, 22 ticks, 50

page table pages, 25 SEG_UCODE, 22 tickslock, 50

panic, 38 SEG_UDATA, 22 timer.c, 40

parent process, 9 seginit, 31 transaction, 67

path, 14 segment descriptor table, 88 trap, 37–38, 41–42, 54

persistence, 67 segment registers, 84 trapret, 20, 22, 38

PGROUNDUP, 29 sequence coordination, 57 traps, 34

physical address, 17, 87 setupkvm, 21–22, 28, 30 tvinit, 36

PHYSTOP, 28–29 signal, 65 type cast, 29

unlink, 72

user memory, 17

user mode, 34

user space, 7

userinit, 21–23

ustack, 31

V2P_WO, 20

vectors[i], 36

virtual address, 17, 88

wait channel, 58

wait, 9, 56, 62

wakeup, 41, 50, 58, 60–61

wakeup1, 61

walkpgdir, 28, 31

write, 72, 78

writei, 73, 76–77

xchg, 48, 50

yield, 54–56

ZOMBIE, 62

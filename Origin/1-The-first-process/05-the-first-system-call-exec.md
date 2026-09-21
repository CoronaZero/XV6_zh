# The first system call: exec

> 来源：book-rev7.pdf，第 17–24 页（英文原文）

The first action of initcode.S is to invoke the exec system call. As we saw in Chapter 0, exec replaces the memory and registers of the current process with a new program, but it leaves the file descriptors, process id, and parent process unchanged.

Initcode.S (7708) begins by pushing three values on the stack— $argv, $init, and $0 —and then sets %eax to SYS_exec and executes int T_SYSCALL: it is asking the kernel to run the exec system call. If all goes well, exec never returns: it starts running the program named by $init, which is a pointer to the NUL-terminated string /init (7721-7723). If the exec fails and does return, initcode loops calling the exit system call, which definitely should not return (7715-7719).

The arguments to the exec system call are $init and $argv. The final zero makes this hand-written system call look like the ordinary system calls, as we will see in Chapter 3. As before, this setup avoids special-casing the first process (in this case, its first system call), and instead reuses code that xv6 must provide for standard operation.

Chapter 2 will cover the implementation of exec in detail, but at a high level it will replace initcode with the /init binary, loaded out of the file system. Now initcode (7700) is done, and the process will run /init instead. Init (7810) creates a new console device file if needed and then opens it as file descriptors 0, 1, and 2. Then it loops, starting a console shell, handles orphaned zombies until the shell exits, and repeats. The system is up.

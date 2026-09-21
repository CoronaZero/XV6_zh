# 代码：exec

> 来源：book-rev7.pdf，第 25–32 页（中文翻译）

exec 是创建地址空间用户部分的系统调用。它从文件系统中存储的文件初始化地址空间的用户部分。exec (5910) 使用 namei (5920) 打开指定的二进制路径，namei 在第 6 章解释。然后，它读取 ELF 头部。xv6 应用程序用广泛使用的 ELF 格式描述，定义在 elf.h 中。一个 ELF 二进制由一个 ELF 头部 struct elfhdr (0955) 后跟一系列程序段头 struct proghdr (0974) 组成。每个 proghdr 描述应用程序中必须加载到内存的一个段；xv6 程序只有一个程序段头，但其他系统可能为指令和数据分别设置段。

第一步是快速检查该文件是否很可能是一个 ELF 二进制文件。ELF 二进制以四个字节的“魔数”0x7F、'E'、'L'、'F' 开头，即 ELF_MAGIC (0952)。如果 ELF 头具有正确的魔数，exec 就假定该二进制格式良好。

exec 用 setupkvm (5931) 分配一张没有用户映射的新页表，用 allocuvm (5943) 为每个 ELF 段分配内存，并用 loaduvm (5945) 把每个段加载到内存中。allocuvm 检查请求的虚拟地址是否低于 KERNBASE。loaduvm (1818) 用 walkpgdir 找到分配内存的物理地址，将 ELF 段的每一页写入该处，并用 readi 从文件读取。

/init（第一个用 exec 创建的用户程序）的程序段头如下：

```c
# objdump -p _init
_init: file format elf32-i386
Program Header:
LOAD off 0x00000054 vaddr 0x00000000 paddr 0x00000000 align 2**2
filesz 0x000008c0 memsz 0x000008cc flags rwx
```

程序段头的 filesz 可能小于 memsz，表示两者之间的空隙应填零（用于 C 全局变量）而不是从文件读取。对于 /init，filesz 是 2240 字节，memsz 是 2252 字节，因此 allocuvm 分配足够的物理内存来容纳 2252 字节，但只从文件 /init 读取 2240 字节。

现在 exec 分配并初始化用户栈。它只分配一页栈。exec 将参数字符串逐个复制到栈顶，并在 ustack 中记录指向它们的指针。它在将要传递给 main 的 argv 列表末尾放置一个空指针。ustack 中的前三个条目是伪装的返回 PC、argc 和 argv 指针。

exec 在栈页正下方放置一个不可访问的页，因此试图使用超过一页的程序会触发故障。这个不可访问页还让 exec 能够处理过大的参数；在这种情况下，exec 用来把参数复制到栈的 copyout 函数会发现目标页不可访问，并返回 –1。

在准备新内存映像的过程中，如果 exec 检测到类似无效程序段的错误，它会跳转到标签 bad，释放新映像，并返回 –1。exec 必须等到确认系统调用会成功，才释放旧映像：如果旧映像已消失，系统调用无法向其返回 –1。exec 中的错误情况只发生在创建映像期间。映像一旦完成，exec 就可以安装新映像 (5989) 并释放旧映像 (5990)。最后 exec 返回 0。
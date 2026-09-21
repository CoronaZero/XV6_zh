# Exercises

> 来源：book-rev7.pdf，第 67–81 页（英文原文）

1. why panic in balloc? Can we recover? 2. why panic in ialloc? Can we recover? 3. inode generation numbers. 4. Why doesn’t filealloc panic when it runs out of files? Why is this more common and therefore worth handling? 5. Suppose the file corresponding to ip gets unlinked by another process between sys_link ’s calls to iunlock(ip) and dirlink. Will the link be created correctly? Why or why not? 6. create makes four function calls (one to ialloc and three to dirlink) that it requires to succeed. If any doesn’t, create calls panic. Why is this acceptable? Why can’t any of those four calls fail? 7. sys_chdir calls iunlock(ip) before iput(cp->cwd), which might try to lock cp->cwd, yet postponing iunlock(ip) until after the iput would not cause deadlocks. Why not?

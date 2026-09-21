# Exercises

> 来源：book-rev7.pdf，第 53–66 页（英文原文）

1. Sleep has to check lk!= &ptable.lock to avoid a deadlock (2567-2570). It could eliminate the special case by replacing

```c
if(lk != &ptable.lock){
acquire(&ptable.lock);
release(lk);
}
```

with

```c
release(lk);
acquire(&ptable.lock);
```

Doing this would break sleep. How? 2. Most process cleanup could be done by either exit or wait, but we saw above that exit must not free p->stack. It turns out that exit must be the one to close the open files. Why? The answer involves pipes. 3. Implement semaphores in xv6. You can use mutexes but do not use sleep and wakeup. Replace the uses of sleep and wakeup in xv6 with semaphores. Judge the result.

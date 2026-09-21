# Sleep and wakeup

> 来源：book-rev7.pdf，第 53–66 页（英文原文）

Locks help CPUs and processes avoid interfering with each other, and scheduling helps processes share a CPU, but so far we have no abstractions that make it easy for processes to communicate. Sleep and wakeup fill that void, allowing one process to sleep waiting for an event and another process to wake it up once the event has happened. Sleep and wakeup are often called sequence coordination or conditional synchronization mechanisms, and there are many other such mechanisms in the operating systems literature.

To illustrate what we mean, let’s consider a simple producer/consumer queue. This queue is similar to the one used by the IDE driver to synchronize a processor and device driver (see Chapter 3), but abstracts all IDE-specific code away. The queue allows one process to send a nonzero pointer to another process. Assuming there is only one sender and one receiver and they execute on different CPUs, this implementation is correct:

```c
100 struct q {
101 void *ptr;
102 };
103
104 void*
105 send(struct q *q, void *p)
106 {
107 while(q->ptr != 0)
108 ;
109 q->ptr = p;
110 }
111
112 void*
113 recv(struct q *q)
114 {
115 void *p;
116
117 while((p = q->ptr) == 0)
118 ;
119 q->ptr = 0;
120 return p;
121 }
```

Send loops until the queue is empty (ptr == 0) and then puts the pointer p in the queue. Recv loops until the queue is non-empty and takes the pointer out. When run in different processes, send and recv both edit q->ptr, but send only writes to the pointer when it is zero and recv only writes to the pointer when it is nonzero, so they do not step on each other.

The implementation above may be correct, but it is expensive. If the sender sends rarely, the receiver will spend most of its time spinning in the while loop hoping for a pointer. The receiver’s CPU could find more productive work if there were a

![Figure 5-2](../../PIC/Figure-5-2.png)

Figure 5-2. Example lost wakeup problem

way for the receiver to be notified when the send had delivered a pointer.

Let’s imagine a pair of calls, sleep and wakeup, that work as follows.

| Function | Description |
| --- | --- |
| Sleep(chan) | sleeps on the arbitrary value chan, called the wait channel. Sleep puts |

the calling process to sleep, releasing the CPU for other work. Wakeup(chan) wakes all processes sleeping on chan (if any), causing their sleep calls to return. If no processes are waiting on chan, wakeup does nothing. We can change the queue implementation to use sleep and wakeup:

```c
201 void*
202 send(struct q *q, void *p)
203 {
204 while(q->ptr != 0)
205 ;
206 q->ptr = p;
207 wakeup(q); /* wake recv */
208 }
209
210 void*
211 recv(struct q *q)
212 {
213 void *p;
214
215 while((p = q->ptr) == 0)
216 sleep(q);
217 q->ptr = 0;
218 return p;
219 }
```

Recv now gives up the CPU instead of spinning, which is nice. However, it turns out not to be straightforward to design sleep and wakeup with this interface without suffering from what is known as the ‘‘lost wake up’’ problem (see Figure 5-2). Suppose that recv finds that q->ptr == 0 on line 215 and decides to call sleep. Before recv can sleep (e.g., its processor received an interrupt and the processor is running the interrupt handler, temporarily delaying the call to sleep), send runs on another CPU: it changes q->ptr to be nonzero and calls wakeup, which finds no processes sleeping and thus does nothing. Now recv continues executing at line 216: it calls sleep and goes to sleep. This causes a problem: recv is asleep waiting for a pointer that has already arrived. The next send will sleep waiting for recv to consume the pointer in the queue, at which point the system will be deadlocked.

The root of this problem is that the invariant that recv only sleeps when q->ptr == 0 is violated by send running at just the wrong moment. One incorrect way of protecting the invariant would be to modify the code for recv as follows:

```c
300 struct q {
301 struct spinlock lock;
302 void *ptr;
303 };
304
305 void*
306 send(struct q *q, void *p)
307 {
308 acquire(&q->lock);
309 while(q->ptr != 0)
310 ;
311 q->ptr = p;
312 wakeup(q);
313 release(&q->lock);
314 }
315
316 void*
317 recv(struct q *q)
318 {
319 void *p;
320
321 acquire(&q->lock);
322 while((p = q->ptr) == 0)
323 sleep(q);
324 q->ptr = 0;
325 release(&q->lock);
326 return p;
327 }
```

This solution protects the invariant because when going calling sleep the process still holds the q->lock, and send acquires that lock before calling wakeup. sleep will not miss the wakeup. However, this solution has a deadlock: when recv goes to sleep it holds on to the lock q->lock, and the sender will block when trying to acquire that lock.

This incorrect implementation makes clear that to protect the invariant, we must change the interface of sleep. Sleep must take as argument the lock that sleep can release only after the calling process is asleep; this avoids the missed wakeup in the example above. Once the calling process is awake again sleep reacquires the lock before returning. We would like to be able to have the following code:

```c
400 struct q {
401 struct spinlock lock;
402 void *ptr;
403 };
404
405 void*
406 send(struct q *q, void *p)
407 {
408 acquire(&q->lock);
409 while(q->ptr != 0)
410 ;
411 q->ptr = p;
412 wakeup(q);
413 release(&q->lock);
414 }
415
416 void*
417 recv(struct q *q)
418 {
419 void *p;
420
421 acquire(&q->lock);
422 while((p = q->ptr) == 0)
423 sleep(q, &q->lock);
424 q->ptr = 0;
425 release(&q->lock);
426 return p;
427 }
```

The fact that recv holds q->lock prevents send from trying to wake it up between recv ’s check of q->ptr and its call to sleep. Of course, the receiving process had better not hold q->lock while it is sleeping, since that would prevent the sender from waking it up, and lead to deadlock. So what we want is for sleep to atomically release q->lock and put the receiving process to sleep.

A complete sender/receiver implementation would also sleep in send when waiting for a receiver to consume the value from a previous send.

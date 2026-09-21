# Race conditions

> 来源：book-rev7.pdf，第 45–52 页（英文原文）

As an example on why we need locks, consider several processors sharing a single disk, such as the IDE disk in xv6. The disk driver maintains a linked list of the outstanding disk requests (3821) and processors may add new requests to the list concurrently (3954). If there were no concurrent requests, you might implement the linked list as follows:

```c
1 struct list {
2 int data;
3 struct list *next;
4 };
5
6 struct list *list = 0;
7
8 void
9 insert(int data)
10 {
11 struct list *l;
```

![Figure 4-1](../../PIC/Figure-4-1.png)

Figure 4-1. Example race

```c
12
13 l = malloc(sizeof *l);
14 l->data = data;
15 l->next = list;
16 list = l;
17 }
```

Proving this implementation correct is a typical exercise in a data structures and algorithms class. Even though this implementation can be proved correct, it isn’t, at least not on a multiprocessor. If two different CPUs execute insert at the same time, it could happen that both execute line 15 before either executes 16 (see Figure 4-1). If this happens, there will now be two list nodes with next set to the former value of list. When the two assignments to list happen at line 16, the second one will overwrite the first; the node involved in the first assignment will be lost. This kind of problem is called a race condition. The problem with races is that they depend on the exact timing of the two CPUs involved and how their memory operations are ordered by the memory system, and are consequently difficult to reproduce. For example, adding print statements while debugging insert might change the timing of the execution enough to make the race disappear.

The typical way to avoid races is to use a lock. Locks ensure mutual exclusion, so that only one CPU can execute insert at a time; this makes the scenario above impossible. The correctly locked version of the above code adds just a few lines (not numbered):

```c
6 struct list *list = 0;
struct lock listlock;
7
8 void
9 insert(int data)
10 {
11 struct list *l;
12
acquire(&listlock);
13 l = malloc(sizeof *l);
14 l->data = data;
15 l->next = list;
16 list = l;
release(&listlock);
17 }
```

When we say that a lock protects data, we really mean that the lock protects some collection of invariants that apply to the data. Invariants are properties of data structures that are maintained across operations. Typically, an operation’s correct behavior depends on the invariants being true when the operation begins. The operation may temporarily violate the invariants but must reestablish them before finishing. For example, in the linked list case, the invariant is that list points at the first node in the list and that each node’s next field points at the next node. The implementation of insert violates this invariant temporarily: line 13 creates a new list element l with the intent that l be the first node in the list, but l ’s next pointer does not point at the next node in the list yet (reestablished at line 15) and list does not point at l yet (reestablished at line 16). The race condition we examined above happened because a second CPU executed code that depended on the list invariants while they were (temporarily) violated. Proper use of a lock ensures that only one CPU at a time can operate on the data structure, so that no CPU will execute a data structure operation when the data structure’s invariants do not hold.

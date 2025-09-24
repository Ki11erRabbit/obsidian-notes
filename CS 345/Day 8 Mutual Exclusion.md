
1. No two processes may be simultaneously inside their critical section

On a single core machine, disabling interrupts guarantees mutual exclusion?
- Yes. No timer interrupt -> no yield

Doesn't work on multiple core machines

## Primitives
- lock (mutex)
- Semaphore
- Monitor
- Message-passing

Can be in kernel or in user space

### Spin Lock
- Pros
	- Very fast, lightweight
- Cons:
	- not suitable for cases where lock needs to be held for a very long
	- Locking thread must _not_ become inactive in any way

### Deadlock
- threads get stuck in a circular wait that will never resolve




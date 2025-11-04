
## Problems with Concurrency
Use tools to reason with concurrency
#### Race Conditions

#### Difficult to reason about correctness of code
presume that any possible sequence of instructions that can occur, will occur



## Locks
Locks protect invariants

#### Atomicity
can't release locks in the middle steps that are intended to be atomic

### Using Locks
Hard to get right

### Semaphores
Maintains a count of "tokens" or "permissions"
P(n) / acquire(n)
V(n) / release(n)

## Reader/Writer Lock
many operations are read-dominant
write forces all reads to lock


## Monitors
easier to use than locks
protect access to concurrently-accessed data

## Lock-free Data Structures / Atomics
use atomics to increase concurrency

# Locks/Increased Parallelism

steal from free list, then hand it back to prevent starvation

name the locks properly
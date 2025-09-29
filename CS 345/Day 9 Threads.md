__thread__: smallest unit of execution supported by tho operating system, has its own register state and stack

Process user thread
- user register state & stack

Process kernel thread
- kernel side companion to process user thread

"Always two there are", a process user thread and process kernel thread

#### Powers of 2
10: kilo
20: mega
30: giga
40: tera
50: peta
60: exa

# Threads
Parallelism within a process

#### Benefits
- lighter weight than process
- enables background tasks
- CPU-bound and I/O-bound can run in parallel
- Exploit multiple cores
- Easier and more efficient than fork() IPC

requires kernel-side support
need to be able to make system calls independently
need to be able to process interrupts
possibly multiple user-side heaps

## User Threads
kernel threads scale better than processes
at extreme scale, they have problems
- stacks consume a lot of memory


## Hybrid Threads
- multiplex user threads onto multiple kernel threads
#### Benefits
Extremely fast
lightweight
scalable

#### Drawbacks
can still suffer from blocking

examples:
- go routines
- java fibers

## Implementing Threads


# Project
Three options



Previous frame pointer is -16 from the current frame pointer


#### Types of Scheduling
#### Batch
- job oriented
used by super computers
##### Two Types
- first-come, first serve
##### Shortest job first
optimal when all run times are known up front
#### Interactive
#### Real-time
Deadlines must be met or something very bad may happen

# Multiprogramming

## xv6 Multiplexing
Two cases
#### Sleep/Wakeup
- device I/O
- Pipe I/O
- wait for child to exit
- sleep system call

Time quantum forced multiplexing

### Challenges
1. How to switch from one process to another?
2. How to force switches in a way that is transparent to processes?
3. Avoiding races when using a common process pool for all CPUs
4. Freeing process resources
5. Each core must track the process that is executing
6. Avoid lost wakeup notifications

### Context Switching
Switch a running process to a runnable process

#### swtch
Objective: Switch from old context to new context

only saves callee-saved registers (because gcc won't do it for us)


# Policy
xv6 is simply picks the next RUNNABLE process (round robin)
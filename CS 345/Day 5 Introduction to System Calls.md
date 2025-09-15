#### Supervisor Mode CSRs
`stvec` - supervisor trap handler base address

`sepc` - supervisor exception program counter
`scause` - supervisor trap cause


### Memory Model
#### Sequential consistency
- Total ordering of memory operations
- total order respects program order
- loads return value written by latest store

- RISC-V is *not* sequentially consistent 
	- uses a write buffer to queue writes back to memory
- to ensure consistency
	- use atomic operations
	- use locks


## Application Binary Interface (ABI)

| Register | ABI Name | Description                      | Saver  |
| -------- | -------- | -------------------------------- | ------ |
| x0       | zero     | Hardwired zero                   | ---    |
| x1       | ra       | Return address                   | Caller |
| x2       | sp       | Stack Pointer                    | Callee |
| x3       | gp       | Global Pointer                   | ---    |
| x4       | tp       | Thread Pointer                   | ---    |
| x5-7     | t0-2     | Temporaries                      | Caller |
| x8       | s0/fp    | Saved register/frame pointer     | Callee |
| x9       | s1       | Saved Register                   | Callee |
| x10-x11  | a0-1     | Function arguments/return values | Caller |
| x12-17   | a2-7     | Function arguments               | Caller |
| x18-27   | s2-11    | Saved registers                  | Callee |
| x28-31   | t3-6     | Temporaries                      | Caller |
- xv6 uses tp to store hartid/cpuid/coreid


# Introduction to System Calls


## Isolation
#### Kernel
- supports multiplexing
- isolates applications from itself and each other


## What is a System Call
- request to the kernel sent from processes


### System Call Implementation
- applications request kernel service through a system call
- similar to function call
- CPU changes to supervisor mode
- processes request
- returns to User Mode

## Trampoline
Trampoline page mapped to top of all virtual address spaces
- same everywhere
- allows us to change virtual address spaces
- read only

## Trap Frame
- trap frame contains per process state
- each process has a unique trap frame
Stores:
- user context during trap handling
- kernel data while user code executes
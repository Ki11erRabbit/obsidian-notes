
Focus on midterm first then work on the lab

# COW Lab

have cow bit 
have a reference count on each page. Use this to see how many processes have read access to the memory

add a page lock

# Midterm Review

Can bring in 10 double sided pages of notes, doesn't need to be handwritten

We will be handed 5 pages of reference material + much of the source code of Xv6

Assume most questions are about XV6

Know how sleep and waking work in XV6, especially with pipes. Understand how it works at a high level

know how multiplexing works, processes interact in Xv6, filesystem

API vs system calls: say what the advantages and disadvantages are

Understand the point of the paper "A fork in the road"

know about kernel architectures

know about the CSR registers for RISC-V

know every line on Switch.S and Trampoline.S

know how traps and timer interrupts work.

Know proc functions
especially allocproc

Threads.
Only care about kernel threads for XV6

know about lock types (spin lock, sleep lock)

now about inter_on and intr_off
push_off/pop_off : keep track of lock depth
know deadlock. Know that it is because of a resource cycle requirement

Know that Linux puts the kernel data in userspace

Know about page tables address lookup
know about superpages
Know about PPN lookup shift right 12

know about direct mapping of physical memory for the kernel

TLB caching
page tables allow for smaller TLB
Know about ASID
sfence.vma flushes the TLB in XV6

Virtual Memory Tricks
know how copyin and copyout work

Storage Allocation
Objectives
internal fragmentation
external fragmentation
buddy system

Know how Context Switching works
swtch

Know how system calls work
`ecall` `sret` for switching process mode
interrupts can also make process mode to change
as well as faults


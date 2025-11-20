# Lab

0 \= operating system picks where to place virtual address
Only care about read an write prot flags

flags
need to support private and shared

can ignore offset

shared \= writes are sent back to the file
private \= changes don't need to be sent back to the file

you can write to private for read only but not shared if it is read only


## Mmap
pick somewhere that is safe
maybe grow down from the trapframe
start from kernbase is also good

don't need to worry about filling in space

## VMA
"Virtual Memory Area"
contiguous space range
same permissions
backed by same object (the file)

### VMA Implementation
multiple ways to implement this

#### Lab suggests
Implemented like process table
linear scan for available
lock to protect
once you have marked the VMA as in use, you can release the lock
zero out VMAs not in use

process has pointers into it

#### Each process has 16 VMAs each


#### VMA Mutation
Support change start, end
only getting shorter

no need to support: punching a hole in the middle

You will likely need to remember the original address so that you can compute the offset in the file

This is for munmap

should put mmap things in vma.c but anywhere is fine.

### Trap Implementation
must lazily allocate memory

Handle errors that don't require VMA

Next, look up VMA
- fault if error
- construct memory mapping if necessary (this is demand paging!) read in data if necessary

supervisor bits or scan vmas

allocate new vmas for `fork`
(you can share them after completing the lab)

don't share VMAs or Pages

guard against bad input

good idea to put in sanity checks



define error codes for mmap
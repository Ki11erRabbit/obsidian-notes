
Can use unused RAM for page cache


# Page Replacement Algorithms
- optimal, load in future data, unload used data
- fifo, anything that is referenced constantly accessed will cause issues
- LRU (least recently used), only remove things that haven't be used recently


# File I/O vs. Memory-Mapped I/O

#### Traditional File I/O
very expensive

#### Memory Mapped I/O
painful to set up but you are just writing to memory
better when writes are small and random

# Copyin Copyout
manually walks the pagetable to find the physical address from userspace to physical memory
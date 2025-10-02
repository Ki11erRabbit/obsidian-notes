
[[Day 9 Threads#Powers of 2]]

2^12 is page size

## Memory Management Unit (MMU)
CPU can read only physical RAM


### Early Approaches
#### Base and Limit
Load user process at address X (base)
Add X to every operation
Make sure every operation does not exceed Y (the limit)
##### Drawback
- overhead
- fragmentation


### Paging
Divides memory into fixed size "pages"
Each page can be:
- mapped to physical memory
- assigned permissions

#### RISC-V Address Spaces
Multiple Schemes
SvX
where X is the number of bits in the address space
|  |  |       |
|--|--|--|
|Sv32| 4 GB | 2-level page table|

##### Sv39
39-bit addresses
High-order bit is sign extended
two regions of valid addresses
separated by a region of invalid/unused addresses
**xv6 only uses 38 bits to avoid dealing with the two regions and sign extension

#### RISC-V Address Translation

39-bits virtual memory to 56-bit physical memory

27 bits index
12 bits offset - same in physical memory

Index into Page Table Entry (PTE)

PTE has Physical Page Number (PPN) and flags

use PPN and Offset to find physical address

- problem, 1 GB per process

##### Solution
Page Table as a tree
multiple levels of translation

L2 9 bits L1 9 bits  L0 9 bits (Offset 12-bits)

L2 -> PPN -> Page Directory
L1 -> PPN -> Page Directory
L0 -> PPN -> Use with offset to find physical page number

satp register points to physical memory that holds the page directory

###### Example
0x007000C100
satp: 0x007
L2 : 0x1 -> PPN 0x0001
L1: 0x180 -> PPN 0x0005
L0: 0x00C -> PPN 0x0003

Physical Address: 0x0000003100

Problem: Performance

##### Problem Performance
Needs to be done every operation


##### Performance Solution: Cache the Page Table
"Translation Lookaside Buffer" - cache TLB entries
Lookup can be done in one cycle
context switch requires flushing TLB
RISC_V supports tagging TLB entries to avoid flushing entire TLB


### Page Table Entry (PTE)
| 63      54 | 53 10 | 9               8                  | 7                                         | 6                                          | 5                                  | 4                                              | 3    | 2     | 1    | 0     |
| :--------: | ----- | ---------------------------------- | ----------------------------------------- | ------------------------------------------ | ---------------------------------- | ---------------------------------------------- | ---- | ----- | ---- | ----- |
|  Reserved  | PPN   | Supervisor Software                | Dirty                                     | Accessed                                   | Global                             | User                                           | Exec | Write | Read | Valid |
|            |       | Can do whatever you want with them | Has this been written to since last clear | Has the page been touched since last clear | Exists in every single page table. | Who can access it. XOR. User or supervisor can |      |       |      |       |

###### Permissions for
- Page Directory: Read, Write, Exec would all be marked 0
- Guard Page: Mark it as invalid
- Trampoline Page:
- Trap Frame:
- Kernel Code:


CPU only works on virtual addresses

### Kernel Address Space
Direct map all usable physical memory into the kernel's virtual address space



# Lab Page Tables/Demand Paging

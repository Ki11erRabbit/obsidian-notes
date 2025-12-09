# Security
## "CIA" Triad
- confidentiality
- integrity
- availability

## Security Principles
economy of mechanism: the simplier, the mechanism the less vulnerabilities it may have

complete mediation: cover everything

principle of least authority: have the least authority to do a needed task

privilege separation: separate by what things need to do

open design: assume adversary has access to source code

psychological acceptability: easy for users to understand

## Protection Domains
matrix of domains
access control lists : like unix file perms
capabilities: like giving a user a bunch of keys

## Cryptography
symmetric keys
asymmetric keys

## Defending against buffer overflows
OS-level:
stack randomization
limit executable regions

## Attacks
use after free: don't use memory after freeing it
null pointer dereference attack: user can control pointer 
time of check to time of use: check then use is not atomic

## Exploiting Hardware
Meltdown: if kernel is mapped into user address space, users can execute code in the kernel. Use kernel value as index into controlled memory and depending on speed determines the value. Fix: don't map kernel into userspace (kernel page table isolation)
Spectre: using speculative execution to execute code that shouldn't be executed. Train speculative execution to execute a certain way. Can't easily prevent

## Exploiting Humans
The weakest link is the human. pretend to be higher up, or find an insider

# IO
Memory Mapped I/O: treat devices as if they are RAM.
DMA: device can directly access system memory
Console Driver: know how to talk to slow devices, Know sleep and wakeup

## Network
Be familiar with all labs

sockets: global vs per socket lock
bind

### Eliminating Receive Livelock
getting stuck receiving more data than processing data
drop packets early than dropping partially processed packets

### Interrupts vs Polling
card interrupts processor vs polling the card
polling has less overhead than interrupts assuming data is ready

use interrupts to start, then use polling to catch anything else that may come in

# Concurrency

Multi-CPU
Single-CPU

need locks because of timer interrupts even if single-core

## Locks
lock implementation
locks protect invariance
when something is in the freelist, no one has a reference to it

easier to make larger locks but this slows down the code

### Thundering herd
coordination when many processes are waiting on one lock and all get woken up at the same time.

### Locks Lab
one vs. multiple free lists
multiple is faster 

# Extending the kernel
## loadable kernel modules
traditionally recompile kernel.
now can dynamically load code into the kernel
no protection against failing kernel module

### ePBF
insert arbitrary code into the kernel.
safe because of restrictions on the code.

# Persistent Storage

## System and process file tables
for every file in the system, it stores every file that is open
process has a file descriptor table that points into the system file table

dup will point a new fd at an already existing file
open will create a new system file table entry

fork will be similar to dup

## HDD
spinning when it will spin

slow seek time

stack of platters
heads move together
each platter has a track around it

concentric tracks are called a cylinder since head can move
track is split into 

most time is spent seeking

good for bulk storage due to cheap per unit volume

## SSD
erase block size is very big and have to do garbage collection and wear leveling to reduce hardware failures

Write amplification, write at the user level can become a bunch of writes on the hardware

## XV6 Layers
File Descriptor
Pathname
Directory: is a file, and is a linear structure that needs to be walked to find valid entries. Slow because of Unix design. Modern filesystems use B-Trees
inode: basically files, name abstracted away, overloaded name. Mirrored in disk and memory
logging: fs logging. Write is made via the log and log is executed all at once. Only committed when the commit record is made. Write back the log if the commit record made if there was a crash. If there is a crash, try it again until it succeeds
buffer cache: synchronize access to disk blocks. One authoritative copy in memory. Act as a cache so you don't have to look up the file on disk. Use Least recently Used. Doubly linked list, access are moved to the front. need space, pull it off the back
disk
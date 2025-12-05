
# High Speed Networking


## Traditional Networking
network stack in kernel

## Kernel Packet Processing
high per-packet/frame overhead

worked well through 100 Mb/s

Started to break around 1 Gb/s
interrupt processing is too slow

limit roughly 1 million packets / second on typical high-end machine

1 million packets / second
1,500 byte packets
12,000 bit packets
12 Gb/s (gigabits)

## Solution 1: Jumbo Frames
9,000 byte packets
72,000 bit packets

72 Gb/s (gigabytes)

## Solution 2: TCP Offloading
TCP consumes too much CPU, bus bandwidth

on send:
OS sends large packet to NIC
NIC breaks the large packet into packets

on receive
merge incoming small packets into one large packet
send large packet to OS

Benefits:
reduced interrupt rate
fewer bus operations required
less CPU required

Problems:
Need to recompute TCP Checksums
OS view of sent/received packets doesn't reflect reality!
more difficult to debug networking issues


ran out of steam when line rate exceeds 100 Gb/s (gigabits)

## Solution 3: BIG TCP

typical offload limit 64KB

observed throughput 170 Gb/s (as of 2022)

Problem current NICS: 800 Gb/s

power limitations are also a thing


## Kernel Networking Stacks
CPU clock rates are frozen because of temperature limits

impact of latency is increasing

## Solution 4: User-mode Networking
Motivation: kernel can't keep up with network

poll the card

Intel: Data Plane Development Kit (DPDK)

Problem:
overhead of TCP becoming unacceptable
AI workloads a poor fit for TCP
Small "flows"


## Solution 5: RDMA
remote direct memory access


allow a process to put data directly into memory on a remote machine

skips kernel

needs to be a lossless network

### RoCE
RDM of Converged Ethernet

RoCE v1: can't route

RoCE v2:
embed infiniband packet in UDP packet


network requirements
lossless
reliable
no failing devices spamming the network

# RedLeaf
Motivation
user-mode networking is fast but inconvenient
can we get the performance of user-mode networking without the trouble?
can we improve reliability and security

RedLeaf
hardware-based isolation is too expensive
use software based isolation

Long history of using software to enforce isolation
- LISP machines
- SPIN (Modula-3)
- J-Kernel/KaffeOS (Java)
- Singularity (Custom C# dialect)
traditionally garbage collected

Program in Rust and get memory safety without garbage collection

## RedLeaf Domains
Fundamental unit of isolation
everything is built on top of one

## Architecture

microkernel

pointers can't cross domains

Rv6: Unix implementation
shared heap: allows to be safely shared across domains

proxies mediate cross-domain invocation
save state
restore if domain crashes
a trampoline page

## microkernel
memory allocator
buddy allocator in microkernel
users run local slab allocators that get memory from the microkernel

threads
can be started by domains

only handles timing and NMI interrupts (something like divide by zero)

# Summary
current interfaces 800 Gb/s

Network performances demands are exceeding the capability of traditional OS kernels

Current approaches
skip kernel entirely
rewrite network stack in user code
DPDK, RDMA

## Redleaf
Drawbacks:
all userspace programs must be written in purely safe Rust
Page tables are useful for more than just isolation (mmap file)

know how redleaf works at a high level and the drawbacks
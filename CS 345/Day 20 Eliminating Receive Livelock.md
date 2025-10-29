Networking research historically conducted on BSD Unix

Linux inverts BSD driver terminology

## Context
At time of publication
- most CPUs were single core
- most computers only had one CPU
Polling required using the one and only CPU

Interrupt-driven is "lower latency"


## Motivating Applications
FTP, Telnet

Host-based routing
network monitoring
network file service (NFS)

## High-level View of Problem
As packet arrival rate increases, it will saturate the system's ability to process packets
System will spend all time processing packets and not 

## Packet Processing
Packet arrives at NIC
High priority need to get the packet off of the NIC to prevent loss

Interrupt handler copies packet to network processing queue
low priority: we already have the packet in RAM

## Problem
There isn't enough CPU left over for the higher-layer network processing
Higher-layer drops packet

## Problems Identified
Starvation of packet transmission
transmission can be prevented by receiving

## Remedy
Invert the priorities
drop earlier rather than later

poll after receiving an interrupt

# Lab

2 parts

## First part
lab instructions describe how to do it
maybe read the network card documentation

implement transmit and receive for card

### Send

1.  ask card for the ring index which it is expecting the next packet `regs[E1000_TDT]`
2. Sanity check the ring index, panic if it is bad
3. check if the ring is overflowing, look at status at bit `E1000_TXD_STAT_DD` if it hasn't finished sending, return an error
4. otherwise, use kfree() to free the last buffer that was transmitted from that descriptor
5. Fill in the descriptor. Set the necessary cmd flags and stash away a pointer for later freeing, set length, set unused fields to zero
6. finally, update the ring position by adding one to `E1000_TDT % TX_RING_SIZE`
7. if success, return 0, On failure, return -1

### Receive
In a loop

## Second Part UDP

Need to implement UDP receive

Don't forget to deal with endianness

need a structure for 
- port number 
- process that is bound
- queue of pointers to buffers of incoming packets (head tail) 
- lock guard the queue

lab requires up to 4 active bound ports

need one big lock when allocating sockets

break search for free port/socket into function
- holds lock when searching
- shared across all the functions that need to search
- returns index if found
	- and acquires lock for port/socket
- returns -1 if not
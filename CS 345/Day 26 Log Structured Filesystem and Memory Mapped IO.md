# Background
Sprite Operating System (active 1984 - 1992) : Jon Osterhout

Key developments
- log structure filesystem


# Process Migration
Move process from one computer to another
#### Why?
redistribute load
resilience 

#### How to do it?
Pause process
Copy things process need
resume execution

#### What makes it hard?
State outside of process memory
- File descriptors
- network sockets
- shared memory



# Motivation Log Structured Filesystem
CPU speed increasing exponentially
Disk speeds no increasing exponentially

## Assumptions
CPU/disk disparity will continue
Disk access will become write dominant

## Technology trend
### CPU
CPU speed increasing at a nearly exponential rate

### Disk
improvements focused on cost and capacity
transfer time improving slowly

### Memory
Capacity increasing exponentially
Two implications
1. Reads are cached
2. 

## Workload
Design for "office and engineering environments"
- lots of small files
- whole writes
Large files out of scope

## Problems with Existing Unix File Systems
Information spread all over disk
- too many small accesses
- one write can require 5 updates to disk
Writes are synchronous
Metadata synchronous
- and multiple metadata writes per operation

## Key idea
structure the entire file system as a log! (with as few exceptions as possible)

append to the head only

#### Why is this good?
Fast writes, fast reads

#### What is challenging?
Modification

## LFS Structure
Updates are likely to be close together

### LFS Structures
Inodes move around
Inode map mappes inode numbers to location in log

segmented log: segments are big enough that you can just write, low seek time


## Free Space Management
"Most Difficult" element of LFS
Goal: maintain large free extents for writing new data

when log reaches end, space will be fragmented

### Two Options
#### Threading
Just keep on using the empty space but it increases fragmentation

#### Compacting
compact empty space down on a per-segment basis

### Segment Cleaning
First two are boring, last two are more interesting
3 & 4 more critical

#### 1. When should the segment cleaner execute?
1. Constantly at low priority?
2. When disk is full?
3. When idle?

#### 2. How many segments should it clean at a time?

#### 3. Which segments should be cleaned?
1. Most fragmented? Not the best

#### 4. How should live blocks be grouped when they are written out?


## Analysis

write cost \= (total bytes read and written) / (new data written)

\= (N + N\*u + N \* (1 - u)) / (N \* (1 - u))
\= 2 / (1 - u)

## Strong Points
Approached storage from a new direction
attacked the problem from fundamentals
overcame potential show-stoppers

## Weak Points
Did not see the rise of SSDs
File distribution no realistic

analysis was small

## Legacy
Landmark work

### Write Anywhere File Layout (WAFL)
inspired by LFS
adopts Copy-on-write
avoids pure logging and cleaning
Snapshots (key feature)
Mirrored volumes etc. also enabled by copy-on-write




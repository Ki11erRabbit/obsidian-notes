
## Stack
LIFO
O(1)
not general - must follow the behavior of stack

## Fixed-size Heap / Free List

internal fragmentation - taking up whole block despite only using a fraction of it.

external fragmentation - slowly splits memory into random unusable chunks

## The buddy system
maintain free lists for all powers of 2
start with only blocks of largest power of 2

binary tree of blocks

on free, buddies are merged if possible

# Midterm

10/16 - 10/20, 10/21 late fee
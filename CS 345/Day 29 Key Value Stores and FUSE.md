
# Key Value Stores

Mapping from keys to values

put
get

## Distributed Key-value store design elements

problem
Probability of failure increases exponentially

## Value Location
O(n) lookup worst case
evenly distributed

Hashing

hash(key) % numServers -> server index
fairly good

bad: adding or removing servers breaks hashing

## Consistent Hashing
MIT/Akamai
Designed to support decentralized cache
- high node churn

data hashed to "ring"

data is hashed and next server on the the ring is the one that "owns" the data

virtual nodes
the more nodes, the more evenly distributed

## Versioning
"bitemporal" versioning: 2D versioning
can use to version data
allows you to go back in time

## Topology Support / Storage Policies
how data should be stored and replicated



# FUSE (File System in User Space)

allows a user-level process to function as a file system

VFS is the api that you implement to be a file system

like a microkernel

## Approach 1: Unix File System
Key-value store becomes a block storage device
implement a direct analog of FFS
- block index -> key
- blocks -> values

## Approach 2: SilverKing File System
Path -> File Attribute (FileID, struct stat, length)
Versioning is 2D

Write 
write a special value to attribute
winner has file locked
version tells us what version to look for in blocks



dctcp ip: tcp ip for data centers
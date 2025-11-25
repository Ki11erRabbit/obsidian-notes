
# File System Features

## Snapshots
Notable feature of WAFL
Automatic
on-demand
stores file history
COW based

## Deduplication
stored data often contains duplicate data
use hashing or data comparison
benefits:
- less storage
costs
- computation required
Generally offered as an out-of-band capability
common with backup systems

## Disk Scrubbing
Data can become bit rotted
scrub the data and compare checksum
repair if possible

## Tiering
create an explicit persistent storage hierarchy
automatically moving between layers
Multiple layers each of
- SSD
- HDD
- Tape

# Network File System (NFS)

## Motivation 
Sun Microsystems
Slogan: "The Network is the Computer"
Local Storage is problematic

### Idea
file server stores data
client access the data

uniform access
individual client device failures inconsequential
Server failures handled centrally where they can be addressed better
- physically secured
- backed up

## NFSv2
Goal: simple crash recovery

clients may crash at any time. Server may crash as well

Key Design: No persistent state

### State
crashes don't matter on either side

### File Handle
- Volume ID
- inode number
- Generation number

### Handling Failure / Idempotent Operations

Idempotent: result of operation is identical each time


### Drawbacks vs. Local File System
Every access is a remote operation

### Improving Performance: Caching


### Caching challenge: Consistency

"Close-to-open consistency"
writes are cached until close
flushed to server
subsequent open calls see the updates

clients use GETADDR to check if they are up to date
- GETADDR storms
- resolve with attribute cache

#### Drawbacks


### Server Implementation
When to ACK writes?

when they are received: crash before writing
when they are on disk: 

### Locks
NFSv2 No locking

some applications require exclusive write access to files

added later
lock server
what happens when client crashes: locked forever
what happens when server crash: locks lost


### Scalability
thousands of servers/tens of thousands of threads access the server


## NFSv4
Stateful
storage industry is now mature
- experience
- capital

the benefits of state outweigh the costs

## Distributed Storage API
File system API
- NFS
- POSIX
- Other?
	- Do we really want to concurrent modification?
		- if so, could we restrict it?

Another API?

## Distributed Systems
State
- hard: server keeps state until client doesn't want it
- soft: server keeps state until timeout
	- timeout
	- refresh
Consensus: 
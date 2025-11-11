
# Directories
Special "File"
inode type `T_DIR`
is structured compared to other inodes

Sequence of directory entries
`inum == 0` not in use

`dirlookup()`
does linear scan of dirents


## Path Name Lookup

`namei()`
given path name, find inode
real work in `namex()`

Concurrency
`iget`/`ilock
only hold one inode at a time

# Fine Descriptor Layer
most resources are a file in Unix
we can use the same code to work with files, console, and pipes

`open()` create a new struct file
`dup()`, `fork()` do not create a new struct

expect System & Process Table on Final

You have your own file offset when you open the file yourself, you don't when you fork off of a process

# File System Lab

change `bmap` and a few constants

`block number (bn)` is less than direct, then access the direct block
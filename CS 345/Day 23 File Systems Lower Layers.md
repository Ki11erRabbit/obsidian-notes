
Store data durably and organized
- can persist after reboot
- file system is organized but not files
allow sharing of data between processes and users

Still being researched
- performance
- concurrency
- recovery
- changing technology

## Design Choices
- objects
- content
- naming


## Buffer Cache

`bread`
gives you a sleep lock already locked

`brelse`
release the lock on buffer


## Inodes
"index node"
overloaded term
abstraction over file
inode is the file and the file name is the label

every file has a unique number called the inode number (also called inode)


# File System Lab
Raise the file size limit

add a double indirect block
double indirect -> double indirect block -> indirect block
							\
							->  indirect block

change `addrs` in `dinode` from 12 to 11

only need to change code in bmap
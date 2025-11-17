
# Bad things an happen
- power failure
- os crash
- reboot (hard, soft)
- hardware failure
	- power
	- motherboard
	- bus
- drive failure
	- controller
	- media
		- corruption
		- total failure
- communication errors
	- bits flipped in transit


# "Scavenging"
Historically file systems didn't worry about consistency in-band
Recovery tools : `fsck`


# Logging/Journaling
first write to log
log is right after super block
once committed, all writes are made to the disk

## Recovery
Three cases
1. No log: nothing to do
2. Log, but no commit: ignore log
3. Log with commit: replay writes

Either all writes appear, or none

Logs can only contain one transaction

Out of Scope for xv6
- disk failure
	- drive
	- controller
	- media
		- bit rot
- corruption in transit


# RAID
## 0
two disks as one

## 1
duplicate disk in another one

## 5
Parity disk
for a few disks

# 6
Two parity disk
for a few disks

# 10 (1 + 0)
two raid 1 drives in raid 0

# End-to-end Principle

how to protect integrity of a message

No matter how reliable everything is, you are only as strong as the weakest link
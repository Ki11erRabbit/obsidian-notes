

# Device Drivers

## Top Half
Interacts with system calls
runs in process kernel thread
handles application requests

## Bottom Half
Lower level. Closer to hardware
run as quickly as possible and exit
defer slow work to top half


# Memory Mapped I/O

Special instructions
- IN \<register\>, \<port\>
- OUT \<port\>, \<regster\>

map a region of physical memory to registers on the device
write to memory -> write to device register


# Production OS Interrupt Balancing
interrupts can be spread across multiple cores
should not cross CPU/NUMA boundaries

### Mapping
#### Static
assign a core to work on the interrupt

#### Daemon
dynamically hands interrupts to cores

# Direct Memory Access (DMA)
## Programmed I/O
CPU reads data from device, writes to RAM
Slow
Everything has to go through CPU

## Direct Memory Access (DMA)
Devices talk directly with RAM
No CPU involvement
faster
CPU can do other work




# Exploiting Software

Buffer overflows
use-after-free 
Null Pointer Dereference
Time of Check to Time of use (TOCTOU)
- check then write later

# Exploiting Hardware
Meltdown
Spectre

## Meltdown
Read from any address that is mapped to the current process's memory space
can be exploited from JavaScript

Speculative execution my address restricted addresses
data cache contains side effects

### Mitigation
Completely separate kernel/user address space
Performance penalty

#### Kernel Page Table Isolation (KPTI)
multiple page tables per process
multiple kernel page tables
don't flush entire page table
use ASID tags (for RISC-V)
## Spectre
entire class of vulnerabilities
much harder than meltdown

train the branch predictor to speculatively execute and give it a bad value

# Exploiting Humans
Social Engineering
Insider Attacks
- logic bombs
- back doors


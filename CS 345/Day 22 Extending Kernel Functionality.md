
# Traditional Unix

Kernel is statically linked

Extending functionality requires
- obtaining new source code
- recompiling source code
- replacing old binary
end user must do this!


# Loadable Kernel Modules

Digital VAX/VMS pioneered loadable kernel modules
loaded at runtime
- added into kernel address space
- initialization called
unloaded at runtime
- cleanup called
- removed from kernel address space

How is this different from a microkernel?
modules still run in kernel mode

#### Good
dynamically extend kernel functionality

#### Bad
Heavyweight
runs in kernel address space
- crash in driver crashes the whole kernel
leading cause of kernel crashes
can access all of kernel
only as good as the worst driver

# Berkeley Packet FIlter (BPF)
allows packets to be captured and filtered at the OS level and get copied into userspace
implementation:
- 32 bits
- fixed-length instructions
- one accumulator
- one index register

# eBPF Virtual Machine
uses a more capable virtual machine than BPF
more registers
- 10 64bit registers
- 1 read-only frame pointer
more instructions

## eBPF Capabilities
can do more than filter packets
various hooks

## Security/Spectre
eBPF originally vulnerable to Spectre attacks

### Hardening eBPF
Bound checks

branch target injection
- retpoline: prevents branch predictor from being trained
Speculative store bypass
- verifier looks for malicious patterns
Constant blinding
- attackers may try to insert code as constants
- "blind" constants
	- store blinded by xor with random number
	- read requires xor again

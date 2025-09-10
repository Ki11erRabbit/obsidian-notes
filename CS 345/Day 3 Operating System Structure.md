#### Objectives
- Multiplexing
- Isolation
- Interaction

# Strawman OS Design: No OS
- user interfaces directly with hardware


## Strawman OS Design: Operating System as Library
- Drawbacks
	- No isolation

# Improved OS Design: "kernel"
- abstracts hardware

## Higher level abstractions
- cores -> processes
- physical RAM -> virtual address space
- disk blocks -> file system

## What is an Operating System?
- provides users with a simpler, cleaner model of the computer
- manages the hardware for the users

### Challenges
- tensions
	- portablity vs fully utilizing bespoke hardware features

### OS Abstracts Hardware Resources
- often through system calls
- fork()/processes abstract cores
	- number of processes not linked to number of cores

### OS Isolates User Processes
- to prevent interference
- also useful in preventing bugs

### Security
- assume user code is malicious and is trying to
	- break isolation
	- manipulate the kernel through system calls
	- interfere with other programs

#### Kernel Isolation from User Code
- hardware-level support
	- CPU is designed with needs of OS in mind
	- Supervisor mode
		- can execute "privileged" instructions
	- User mode
	- memory isolation

#### System Calls
- user applications need access to privileged functions
- potentially unsafe!
- solution
	- Well-defined access points: system calls
- E.g. system_call()
	- ecall
- ecall
	- changes to supervisor mode
	- jumps to well-defined location in the kernel

## System calls vs api over system calls


## Monolithic Kernel

## Layered System/MULTICS
- generalization of user/kernel separation
- Advantages
	- security
- Disadvantages
	- complexity
	- performance

## Monolithic Kernels


## Microkernel
- run the bare minimum in kernel mode
- run as much as possible as a user process
- bug in one component, no longer crashes the kernel
- drawback: performance
- Mach, MINIX, Amoeba

## Hybrid Kernels
- middle ground between pure monolithic and pure microkernel
- performance-critical elements run in the kernel
- other run as user processes

- Mach -> NextSTEP -> macOS
- Windows NT (inspired by Mach)

## Virtual Machines
- key idea
	- multiple copies of simulated hardware, on top of actual hardware
- first implemented over 40 years ago by IBM
- Rediscovered in x86 world

## Containers
- motivation
	- virtual machines are heavy weight
	- sharing a single operating system is lower overhead
- Advantages
	- lightweight
- disadvantages
	- not truly isolated
	- OS Crash will crash all containers


# Add a layer of indirection

# Policy vs mechanism
- policy
	- what is done
- mechanism
	- how it is done

- should be kept separate

# A fork() in the road


# Lab Questions
#### Sleep/tick
- look at interrupt code
- interrupts about ever 100 ms

#### Passing the grading program
- for utils, just pass grading program
- for everything else, don't do things that will crash the kernel

#### find

#### xargs
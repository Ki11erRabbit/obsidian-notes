Presentation by Craig Christianson

SkiCAMP
309 SWEG USAF


# Challenges of Software Engineering
Types of software engineers
- Craftsmen: Creation backed by Skill, experience, best practice
  Throw code at compiler until it compiles, use tests to find bugs
  most software works this way
- Engineers:
  backed by:
	- Mathematically defined specifications
	- mathematically & scientifically verified solutions
	- compliance - follow legal codes
	- liability transfer - whoever verifies design takes some liability of issues

Florida city got hacked and water supply got Lye added to water supply 
it got resolved quickly by someone watching


## Safety Critical Software
- damage
- injure
- kill
someone, it is safety critital software

Requirements for DO178C
- High-level & low-level requirements verification
- bidirectional requirements traceablity
- 100% code coverage tests
- reviews and analyses
- intense testing at each integration level

# Software Assurance
code review for each line
going through each test case
do test case in live environment

room full of engineers is expensive

## Two Roads to Certification
### Legacy Assurance
- systematic evaluation and testing
- essentially an intensive & onerous form of quality assurance

### Formal verification
- uses mathematical models to define systems
- uses proofs to provide traceability

## Assurance: Substantiating Trust

### Legacy
need to provide a compelling argument at each lower level of abstraction
continuous reviews

## Formal Verification
Uses refinement proof for each lower level of abstraction
continuous proof updates

## Automated Reasoning

ISAR Programming Languages
Isabel

### Proof Properties
- machine checkable
- traceablity
- non-ambiguity
- reusability (reuse proofs)
- compositionality (build larger proofs out of smaller proofs)
- sharable (they are just code so sharable)
- soundness (no counter examples)
- completeness (you can't test out to infinity, but you can now reason about infinity)
- repeatable/automatable (can now use machines to check)


# Pancake Verification
A Language for Verified Systems Programming

compiler in trusted computer base (we are choosing to trust the software even when we shouldn't)
pull the compiler out

CakeML
Verified Compiler
Used an automated reasoning language to verify compiler

$1000 per line of code
writing the code is the easy part
verifying is the hard part.

# seL4 Microkernel

Microkernel Minimality Principle:
If there is anything that can exist outside of the kernel, then it must be outside of the kernel.
Liedtke SOSP'95

if driver has vulnerability, then whole system can be compromised

## Trusted Computing Base
### Traditional Systems
- ~25 Million Lines of Code
- ~75% of TCB is device drivers
### seL4
- Drivers in User Space
- ~10,000 Lines of Code
- 900,000 Lines of Proofs

### Formally Verified Properties
- Functional Correctness: behave like the specification says. Never seen a bug in seL4 for many years
  assumes that hardware isn't compromised among other 
- Termination: a sys call is guaranteed to terminate
- Memory Safety
- Integrity: can't modify memory unless you have access
- Confidentiality: can't read memory unless you have access
- Information Flow Security: memory is separated temporally (no timing attacks)
- Determinism: real time

# Modern Software Verification
## Key Take-Aways:
- formal methods 

# Microkit
Build system for seL4

## Protected Domain
first function called is Init
Notified - let a neighboring domain know that you want to notify them
Protected


craig.christianson@us.af.mil
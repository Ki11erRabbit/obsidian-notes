
# "CIA" Triad

- **C**onfidentiality: keep confidential data from being exposed
- **I**ntegrity
- **A**vailablity

## Economy of Mechanism
KISS
more complexity -> more vulnerability
- more room for mistakes
- harder to reason about correctness

Attack surface

If a function isn't required, leave it out


## Fail-safe Defaults
nuclear reactor
- power failure -> control rods stop reaction

Default to no access
- create rules to allow access
- other way around is insecure

## Complete Mediation
every access should be checked for authority

## Least Authority

Similar to "need to know basis" in national security
- you can't expose information that you don't have
	- see Venona project

each component should have only as much authority as it needs
can't compromise capabilities that it doesn't posses

## Privilege Separation
decompose systems into multiple components with minimal privilge

## Open Design
assume attacker has the source code
"Security by obscurity" is insecure
- not necessarily a law


## Psychological Acceptability
security should be easy to understand


## Security principles
there is a tension between the principles


### Java Security Manager
- too large
- huge complexity
- burden on applications
- Java applets failed to catch on

### Protection Domains
#### Objects
Things that we want to control rights to
- device
- file

#### Rights


Domain: has certain abilities

| Domain | File A | File B               | Printer |
| ------ | ------ | -------------------- | ------- |
| 1      | Read   |                      |         |
| 2      | Read   | Read, Write, Execute |         |

#### Access Control List
divide matrix by columns
each object has list of domains and permissions


#### Capabilities
divide matrix by rows
each domain has a list of objects and permissions

## Cryptography
"Secret writing"

## Venona
#### One time pads
Encrypt add (or xor) random pad


unbreakable in theory
length of pad is problematic
can't reuse pad
other person must have the same random pad

# Lab Tips

Start fresh and copy things over. (I don't have things to use)
making a function that does the page fault handling

count references to pages
start counting at 8000
have an array to hold reference count to PHYSTOP - KERNBASE
only free page if reference count goes to 0
protect reference count with a lock so use kmem lock
make functions that can interact with kmem lock that are exposed in defs.h

make `uvmcopy` map and not allocate.
change page to read only
at one reference, can be made writable again
`uvmcopy` is likely where the most work needs to be done
use software bytes of PTE to store if the page was formally writable

make sure to go through all checks to ensure page fault is because of COW and not a access violation

make public and private versions of ref counting, expose only locking version 
private versions are for use when the lock is already held
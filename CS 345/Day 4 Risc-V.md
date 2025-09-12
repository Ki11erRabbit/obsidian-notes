# Risc-V
UC Berkeley
Open ISA
- does not define implementation
- not linked to the fate of a single company
- foundation
Simple
Modular

Appears to be succeeding
- very popular in embedded space

## Risc-V Intruction Types
- R-type (Register)
- I-type (Immediate)
- S-type (Store)
- B-type (Branch)
- J-type (Jump)
- U-type


| Register | ABI Name | Description    | Saver  |
| -------- | -------- | -------------- | ------ |
| x0       | zero     | Hardwired zero | ---    |
| x1       | ra       | Return address | Caller |
| x2       | sp       | Stack Pointer  | Callee |
| x3       | gp       | Global Pointer | ---    |
|          |          |                |        |

word in RISC-V is 32 bits

## Psuedo-Instructions
- nop = addi, x0 x0, 0
- li = li a0, 1
- la = la rd, symbol
- call = call offset
- ret
- not = rd, rs #1's complement
- neg = rd, rs #2's complement
- beqz = rs, offset

## Basic Data Movement Operations



## lui, auipc
Risc-V instructions are only 32 bits, Must use multiple instructions to form full 64-bit values

lui \<register\>, \<imm20\>

- lui x5, 0x12345      # x5 = 0x12345000
- addi x5, x5, 0x678 # x5 = 0x12345678


## jal, jalr
remembers where we came from
- jal rd, offset
- jalr rd, rs1, offset


- jr         jalr x0, rs, 0
- ret       jalr, x0, x1, 0


## Exceptions
- Exceptions
- Trap
- Fault
- Hardware interrupt
- Software interrupt

Naming is hard and some names are not clear.


### Exception
- an unusual or exceptional condition occurring at run time associated with an instruction in the current RISC-V hart (divide by zero)
- Synchronous

### Interrupt
- an external\* asynchronous event that may cause a RISC-V hart to experience an unexpected transfer of control
- hardware/device
- timer
- \*Software: mimics external event

### Trap
exception or interrupt

### Fault
synonym for exception

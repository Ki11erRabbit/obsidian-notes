# Modes
This fantasy pc should have different modes that define the max size that a register can hold.
Ram size should be configurable to allow for at most the max addressable size but can also be lower.

###### 16 bit
The default mode.
This mode is intended to be simpler and easier to use than 32 or 64 bit modes.
You get 4 times as many registers as 64 bit mode because we split the 64 bit registers into quarters.
Max RAM size in this mode is 65,536 bytes.

###### 32 bit
This mode allows addressing up to 4,294,967,296 bytes.
You lose half the amount of registers from 16 bit mode when you use 32 bit values and 2 times as many registers from 64 bit mode.
You also gain access to 32 bit floats.
This mode is intended to provide greater power than 16 bit mode

###### 64 bit mode
This mode allows addressing up to 8,589,934,592 bytes of RAM which is double that of 32 bit mode.
You lose half of the amount of registers as 32 bit mode and quadruple the amount from 16 bit mode.
You retain access to 32 bit floats and gain access to 64 bit floats but they share the same registers

## Mode Features
| Mode      | Integer Registers            | Floating Point Registers | Control Registers                                                                                                                                                                | Misc Registers                                               | Max Cores | Max Cards | Clock Speed | Max Addressable RAM               |
| --------- | ---------------------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ | --------- | --------- | ----------- | --------------------------------- |
| 16 bit    | qr0-qr127 (quarter register) | None                     |                                                                                                                                                                                  |                                                              | 1 Core    | 4         | 8 MHz       | 65,536 bytes (64 kilobytes)       |
| 32 bit    | hr0-hr63 (half register)     | f0-f31 (float)           | floatexn (float exceptions)                                                                                                                                                      | coreid (current core id)                                     | 2 Cores   | 8         | 800 MHz     | 4,294,967,296 bytes (4 gigabytes) |
| 64 bit    | r0-r31 (register)            | d0-d31 (float)           | floatexn (float exceptions)                                                                                                                                                      | coreid (current core id)                                     | 4 Cores   | 16        | 1.6 GHz     | 8,589,934,592 bytes (8 gigabytes) |
| All Modes |                              |                          | intexn (integer exceptions)<br>trap (stores address of trap handlers)<br>trapcause (stores cause of trap)<br>trapval (stores faulting value)<br>inter (1 enables interrupts)<br> | seed (random initial seed 64 bits)<br>zero (always zero)<br> |           |           |             |                                   |

## Control Register Bits
###### intexn
Convert is when a float can't be represented as an int

| 15  | 14  | 13  | 12  | 11  | 10  | 9   | 8   | 7   | 6   | 5   | 4       | 3         | 2        | 1                | 0       |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ------- | --------- | -------- | ---------------- | ------- |
|     |     |     |     |     |     |     |     |     |     |     | Wrapped | Underflow | Overflow | Division By Zero | Convert |
###### floatexn
see IEEE 754 for description of exceptional codes
Convert is when you can't represent an int as a float properly

| 15  | 14  | 13  | 12  | 11  | 10  | 9   | 8   | 7   | 6   | 5       | 4       | 3         | 2        | 1                | 0       |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ------- | ------- | --------- | -------- | ---------------- | ------- |
|     |     |     |     |     |     |     |     |     |     | Inexact | Invalid | Underflow | Overflow | Division By Zero | Convert |
# ISA

| Instruction | Form                                    | Description                                                                                                                     | Example             | Clock Cycles |
| ----------- | --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ------------ |
| li          | li target,immediate                     | Loads an immediate value into a register                                                                                        | li qr20, 42         | 1            |
| lr          | lr target,src                           | Loads a register into another register                                                                                          | lr hr12, hr1        | 1            |
| la          | la target, src address                  | Loads data from memory from an address from a register and places into a register                                               | la r2, r16          | 4            |
| sr          | sr target address, src                  | Stores data from a register into target address register                                                                        | sr r0, r2           | 4            |
| adds        | adds dest,src1,src2                     | Signed Addition<br>dest = src1 + src2<br>This is for integers only                                                              | add qr2, qr9, qr4   | 1            |
| addu        | addu dest,src1,src2                     | Unsigned Addition<br>dest = src1 + src2<br>This is for integers only                                                            |                     | 1            |
| subs        | subs dest,src1,src2                     | Signed Subtraction<br>dest = src1 - src2<br>This is for integers only                                                           | sub hr12, hr1, hr32 | 1            |
| subu        | subu dest,src1,src2                     | Unsigned Subtraction<br>dest = src1 - src2<br>This is for integers only                                                         |                     | 1            |
| muls        | muls dest,src1,src2                     | Signed Multiplication<br>dest = src1 \* src2<br>This is for integers only                                                       |                     | 2            |
| mulu        | mulu dest,src1,src2                     | Unsigned Multiplication<br>dest = src1 \* src2<br>This is for integers only                                                     |                     | 2            |
| divs        | divs dest_rem,dest_result,src1,src2<br> | Signed Division<br>dest_result, dest_rem = src1 / src2<br>Calculates division and remainder.<br>This is for integers only<br>   |                     | 4            |
| divu        | divu dest_rem,dest_result,src1,src2<br> | Unsigned Division<br>dest_result, dest_rem = src1 / src2<br>Calculates division and remainder.<br>This is for integers only<br> |                     | 4            |
| addf        | addf dest,src1,src2                     | Floating Point Addition<br>dest = src1 + src2                                                                                   |                     | 8            |
| subf        | subf dest, src1, src2                   | Floating Point Subtraction<br>dest = src1 - src2                                                                                |                     | 8            |
| mulf        | mulf dest,src1,src2                     | Floating Point Multiplication<br>dest = src1 \* src2                                                                            |                     | 16           |
| divf        | divf dest,src1,src2                     | Floating Point Division<br>dest = src1 / src2                                                                                   |                     | 32           |
| sqrt        | sqrt dest_float, src_float              | Floating Point Square Root<br>dest_float = sqrt(src_float)                                                                      |                     | 32           |
| cfi         | cfi dest_int, src_float                 | Float to Integer Conversion<br>dest_int = int(src_float)<br>This can set the intexn Convert flag on failure                     |                     | 8            |
| cif         | cif dest_float, src_int                 | Integer to Float Conversion<br>dest_float = float(src_int)<br>This can set the floatexn Convert flag on failure                 |                     | 8            |
| or          | or dest, src1, src2                     | Bitwise Or<br>dest = src1 \| src2                                                                                               |                     | 1            |
| and         | and dest, src1, src2                    | Bitwise And<br>dest = src1 & src2                                                                                               |                     | 1            |
| xor         | xor dest, src1, src2                    | Bitwise Exclusive Or<br>dest = src1 ^ src2                                                                                      |                     | 1            |
| compl       | compl dest, src                         | Bitwise Not (Complement)<br>dest = ~src                                                                                         |                     | 1            |
| shar        | shar dest, src, amount                  | Arithmetic Right Shift<br>dest = src \>\> amount                                                                                |                     | 2            |
| shlr        | shlr dest, src, amount                  | Logical Right Shift<br>dest = src \>\> amount                                                                                   |                     | 2            |
| shal        | shal dest, src, amount                  | Arithmetic Left Shift<br>dest = src \<\< amount                                                                                 |                     | 2            |
| shll        | shll dest, src, amount                  | Logical Left Shift<br>dest = src << amount                                                                                      |                     | 2            |
| rotr        | rotr dest, src, amount                  | Rotate Right<br>dest \= rotate_right(src, amount)                                                                               |                     | 2            |
| rotl        | rotl dest, src, amount                  | Rotate Right<br>dest \= rotate_left(src, amount)                                                                                |                     | 2            |
| eq          | eq dest, src1, src2                     | Integer Equality<br>max unsigned on equal, 0 on not equal<br>dest = src1 == src2                                                |                     | 1            |
| lts         | lts dest, src1, src2                    | Signed Less Than<br>max unsigned on less, 0 otherwise<br>dest = src1 < src2                                                     |                     | 1            |
| ltu         | ltu dest, src1, src2                    | Unsigned Less Than<br>max unsigned on less, 0 otherwise<br>dest = src1 < src2                                                   |                     | 1            |
| les         | les dest, src1, src2                    | Signed Less Than or Equal<br>max unsigned on less and equal, 0 otherwise<br>dest = src1 <= src2                                 |                     | 1            |
| leu         | leu dest, src1, src2                    | Unsigned Less Than Or Equal<br>max unsigned on less and equal, 0 otherwise<br>dest = src1 <= src2                               |                     | 1            |
| gts         | gts dest, src1, src2                    | Signed Greater Than<br>max unsigned on greater, 0 otherwise<br>dest = src1 > src2                                               |                     | 1            |
| gtu         | gtu dest, src1, src2                    | Unsigned Greater Than<br>max unsigned on greater, 0 otherwise<br>dest = src1 > src2                                             |                     | 1            |
| ges         | ges dest, src1, src2                    | Signed Greater Than or Equal<br>max unsigned on greater or equal, 0 otherwise<br>dest = src1 >= src2                            |                     | 1            |
| geu         | geu dest, src1, src2                    | Unsigned Greater Than Or Equal<br>max unsigned on greater or equal, 0 otherwise<br>dest = src1 >= src2                          |                     | 1            |
| eqf         | eqf dest_int, src1_float, src2_float    | Floating Point Equality<br>max unsigned on equal, 0 on not equal<br>dest_int = src1_float == src2_float                         |                     | 16           |
| ltf         | ltf dest_int, src1_float, src2_float    | Floating Point Less Than<br>max unsigned on less, 0 otherwise<br>dest_int \= src1_float \< src2_float                           |                     | 16           |
| lef         | lef dest_int, src1_float, src2_float    | Floating Point Less Than or Equal<br>max unsigned on less or equal, 0 otherwise<br>dest_int \= src1_float \< src2_float         |                     | 16           |
| gtf         | gtf dest_int, src1_float, src2_float    | Floating Point Greater Than<br>max unsigned on greater, 0 otherwise<br>dest_int \= src1_float \< src2_float                     |                     | 16           |
| gef         | gef dest_int, src1_float, src2_float    | Floating Point Greater Than or Equal<br>max unsigned on greater or equal, 0 otherwise<br>dest_int \= src1_float \< src2_float   |                     | 16           |
| joff        | joff return_addr, offset                | Jump Forwards or backwards<br>offset should be signed<br>return_addr is the next instruction following the jump instruction     |                     | 1            |
| jto         | jto return_addr, location               | Jump to a Location<br>return_addr is the next instruction following the jump instruction                                        |                     | 1            |
| beq         | beq src1,src2, offset                   | Branch if src1 and src2 are equal<br>offset should be signed                                                                    |                     | 1            |
| beq         | bneq src1, src2, offset                 | Branch if src1 and src2 are not equal<br>offset should be signed                                                                |                     | 1            |
| blts        | blts src1,src2, offset                  | Branch if src1 is less than src2 using signed comparison                                                                        |                     | 1            |
| bltu        | bltu src1,src2, offset                  | Branch if src1 is less than src2 using unsigned comparison                                                                      |                     | 1            |
| bgts        | bgts src1,src2, offset                  | Branch if src1 is greater than src2 using signed comparison                                                                     |                     | 1            |
| bgtu        | bgtu src1, src2, offset                 | Branch if src1 is greater than src2 using unsigned comparison                                                                   |                     | 1            |
| bles        | bles src1, src2, offset                 | Branch if src1 is less than or equal to src2 using signed comparison                                                            |                     | 1            |
| bleu        | bles src1, src2, offset                 | Branch if src1 is less than or equal to src2 using unsigned comparison                                                          |                     | 1            |
| bges        | bges src1, src2, offset                 | Branch if src1 is greater than or equal to src2 using signed comparison                                                         |                     | 1            |
| bgeu        | bgeu src1, src2, offset                 | Branch if src1 is greater than or equal to src2 using unsigned comparison                                                       |                     | 1            |
# Calling Convention

###### 16 bit mode
| Register  | ABI Name  | Description                                                          | Saver  |
| --------- | --------- | -------------------------------------------------------------------- | ------ |
| qr0       | ra        | Return Address                                                       | Caller |
| qr1       | sp        | Stack Pointer                                                        | Callee |
| qr2       | pc        | Program Counter                                                      |        |
| qr3       | fp        | Frame Pointer                                                        | Caller |
| qr4-45    | t0-41     | Temporaries                                                          | Caller |
| qr46-86   | s0-41     | Saved Register                                                       | Callee |
| qr89-127  | a0-a41    | Function Arguments/Return Values                                     | Caller |
| zero      | zero      | Hard-wired zero                                                      |        |
| intexn    | intexn    | Bitflags of integer exceptions                                       |        |
| trap      | trap      | Stores address of trap handler                                       |        |
| trapcause | trapcause | Stores error code for trap reason                                    |        |
| trapval   | trapval   | Stores value that caused trap                                        |        |
| inter     | inter     | Determines if interrupts are enabled or not. (1 enabled, 0 disabled) |        |
| seed      | seed      | Seed value set at startup                                            |        |

###### 32 bit mode

| Register  | ABI Name  | Description                                                          | Saver  |
| --------- | --------- | -------------------------------------------------------------------- | ------ |
| hr0       | ra        | Return Address                                                       | Caller |
| hr1       | sp        | Stack Pointer                                                        | Callee |
| hr2       | pc        | Program Counter                                                      |        |
| hr3       | fp        | Frame Pointer                                                        | Caller |
| hr4-23    | t0-19     | Temporaries                                                          | Caller |
| hr24-43   | s0-19     | Saved Register                                                       | Callee |
| hr44-63   | a0-19     | Function Arguments/Return Values                                     | Caller |
| f0-1      | fret0-1   | FP Return Values                                                     | Caller |
| f2-11     | ft0-9     | FP Temporaries                                                       | Caller |
| f12-21    | fs0-9     | FP Saved                                                             | Callee |
| f22-31    | fa0-9     | FP Function Arguments                                                | Caller |
| zero      | zero      | Hard-wired zero                                                      |        |
| intexn    | intexn    | Bitflags of integer exceptions                                       |        |
| floatexn  | floatexn  | Bitflags of float exceptions                                         |        |
| trap      | trap      | Stores address of trap handler                                       |        |
| trapcause | trapcause | Stores error code for trap reason                                    |        |
| trapval   | trapval   | Stores value that caused trap                                        |        |
| inter     | inter     | Determines if interrupts are enabled or not. (1 enabled, 0 disabled) |        |
| seed      | seed      | Seed value set at startup                                            |        |
| coreid    | coreid    | Current Core ID                                                      |        |

###### 64 bit mode
| Register  | ABI Name  | Description                                                          | Saver  |
| --------- | --------- | -------------------------------------------------------------------- | ------ |
| r0        | ra        | Return Address                                                       | Caller |
| r1        | sp        | Stack Pointer                                                        | Callee |
| r2        | pc        | Program Counter                                                      |        |
| r3        | fp        | Frame Pointer                                                        | Caller |
| r4-13     | t0-9      | Temporaries                                                          | Caller |
| r14-22    | s0-8      | Saved Register                                                       | Callee |
| r23-31    | a0-8      | Function Arguments/Return Values                                     | Caller |
| d0-1      | dret0-1   | FP (double) Return Values                                            | Caller |
| d2-11     | dt0-9     | FP (double) Temporaries                                              | Caller |
| d12-21    | ds0-9     | FP (double) Saved                                                    | Callee |
| d22-31    | da0-9     | FP (double) Function Arguments                                       | Caller |
| zero      | zero      | Hard-wired zero                                                      |        |
| intexn    | intexn    | Bitflags of integer exceptions                                       |        |
| floatexn  | floatexn  | Bitflags of float exceptions                                         |        |
| trap      | trap      | Stores address of trap handler                                       |        |
| trapcause | trapcause | Stores error code for trap reason                                    |        |
| trapval   | trapval   | Stores value that caused trap                                        |        |
| inter     | inter     | Determines if interrupts are enabled or not. (1 enabled, 0 disabled) |        |
| seed      | seed      | Seed value set at startup                                            |        |
| coreid    | coreid    | Current Core ID                                                      |        |
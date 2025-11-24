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
| Mode      | Integer Registers            | Floating Point Registers | Control Registers                                                                                                                                                                                                                                                                            | Misc Registers                         | Max Cores | Max Cards | Clock Speed | Max Addressable RAM               |
| --------- | ---------------------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------- | --------- | --------- | ----------- | --------------------------------- |
| 16 bit    | qr0-qr127 (quarter register) | None                     |                                                                                                                                                                                                                                                                                              |                                        | 1 Core    | 4         | 8 MHz       | 65,536 bytes (64 kilobytes)       |
| 32 bit    | hr0-hr63 (half register)     | f0-f31 (float)           | floatexn (float exceptions)                                                                                                                                                                                                                                                                  | coreid (current core id)               | 2 Cores   | 8         | 800 MHz     | 4,294,967,296 bytes (4 gigabytes) |
| 64 bit    | r0-r31 (register)            | d0-d31 (float)           | floatexn (float exceptions)                                                                                                                                                                                                                                                                  | coreid (current core id)               | 4 Cores   | 16        | 1.6 GHz     | 8,589,934,592 bytes (8 gigabytes) |
| All Modes |                              |                          | returnaddr (return address)<br>progcount (program counter)<br>intexn (integer exceptions)<br>trap (stores address of trap handlers)<br>trapcause (stores cause of trap)<br>trapval (stores faulting value)<br>inter (1 enables interrupts)<br>stackptr (stack pointer)<br>zero (always zero) | seed (random initial seed 64 bits)<br> |           |           |             |                                   |

## Control Register Bits
###### intexn
Convert is when a float can't be represented as an int

| 15  | 14  | 13  | 12  | 11  | 10  | 9   | 8   | 7   | 6   | 5   | 4       | 3       | 2         | 1        | 0                |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ------- | ------- | --------- | -------- | ---------------- |
|     |     |     |     |     |     |     |     |     |     |     | Convert | Wrapped | Underflow | Overflow | Division By Zero |
###### floatexn
see IEEE 754 for description of exceptional codes
Convert is when you can't represent an int as a float properly

| 15  | 14  | 13  | 12  | 11  | 10  | 9   | 8   | 7   | 6   | 5       | 4       | 3       | 2         | 1        | 0                |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ------- | ------- | ------- | --------- | -------- | ---------------- |
|     |     |     |     |     |     |     |     |     |     | Convert | Inexact | Invalid | Underflow | Overflow | Division By Zero |
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
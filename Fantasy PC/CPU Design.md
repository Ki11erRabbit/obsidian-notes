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
| Mode      | Integer Registers            | Floating Point Registers | Control Registers                                                                                                                                                                                                                                                      | Misc Registers                         | Max Cores | Max Cards | Clock Speed | Max Addressable RAM               |
| --------- | ---------------------------- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------- | --------- | --------- | ----------- | --------------------------------- |
| 16 bit    | qr0-qr127 (quarter register) | None                     |                                                                                                                                                                                                                                                                        |                                        | 1 Core    | 4         | 8 MHz       | 65,536 bytes (64 kilobytes)       |
| 32 bit    | hr0-hr63 (half register)     | f0-f31 (float)           | floatexn (float exceptions)                                                                                                                                                                                                                                            | coreid (current core id)               | 2 Cores   | 8         | 800 MHz     | 4,294,967,296 bytes (4 gigabytes) |
| 64 bit    | r0-r31 (register)            | f0-f31 (float)           | floatexn (float exceptions)                                                                                                                                                                                                                                            | coreid (current core id)               | 4 Cores   | 16        | 1.6 GHz     | 8,589,934,592 bytes (8 gigabytes) |
| All Modes |                              |                          | returnaddr (return address)<br>progcount (program counter)<br>intexn (integer exceptions)<br>trap (stores address of trap handlers)<br>trapcause (stores cause of trap)<br>trapval (stores faulting value)<br>inter (1 enables interrupts)<br>stackptr (stack pointer) | seed (random initial seed 64 bits)<br> |           |           |             |                                   |
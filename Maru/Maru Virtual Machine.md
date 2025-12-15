

# Maru File

A maru file is like Java's class file, it contains bytecode needed to be executed as well as typing data, strings, and ways to map bytecode to source code lines and columns

## File Format
All in pseudo Rust 


```Rust
#[repr(C)]
struct MaruFile {
	magic: u8, // 0
	major_ver: u8,
	minor_ver: u8,
	patch_ver: u8,
	module_name: StringIndex,
	object_types: [MaruObject],
	functions: [MaruFunction],
	globals: [MaruGlobal],
	string_table: StringTable,
	bytecode_table: BytecodeTable,
	location_map: LocationMap,
}

#[repr(C)]
struct MaruObject {
	name_index: StringIndex,
	type_name: StringIndex,
	variants: [MaruVariant]
}

#[repr(C)]
struct MaruVariant {
	name_index: StringIndex,
	members: [(StringIndex, TypeTag)],
}

#[repr(C)]
struct MaruFunction {
	name_index: StringIndex,
	type_name: StringIndex,
	return_type: TypeTag,
	arg_types: [TypeTag],
	bytecode_index: BytecodeIndex,
	variables: u32,
}

#[repr(C)]
struct MaruGlobal {
	name_index: StringIndex,
	typ: TypeTag,
	bytecode_index: BytecodeIndex,
}

#[repr(C)]
struct StringTable {
	table: [(u32, str)]
}

#[repr(C)]
/// Zero Indexed
struct BytecodeTable {
	table: [[u8]]
}

#[repr(C)]
/// Mirrors the BytecodeTable
struct LocationMap {
	map: [[(u32, u32)]]
}

enum TypeTag {
	U8,
	I8,
	U16,
	I16,
	U32,
	I32,
	U64,
	I64,
	F32,
	F64,
	Object(StringIndex),
}

```


## Bytecode

| Instruction    | Form                                        | Arguments                                                                                                                                                         | Explanation                                                                                                                                                                                                                  |
| -------------- | ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Load8          | load8 reg, imm                              | reg: dest register<br>imm: immediate value                                                                                                                        | Loads a byte into a registers                                                                                                                                                                                                |
| Load16         | load16 reg, imm                             | reg: dest register<br>imm: immediate value                                                                                                                        | Loads a 16bit value into a register                                                                                                                                                                                          |
| Load32         | load32 reg, imm                             | reg: dest register<br>imm: immediate value                                                                                                                        | Loads a 32bit value into a register                                                                                                                                                                                          |
| Load64         | load64 reg, imm                             | reg: dest register<br>imm: immediate value                                                                                                                        | Loads a 64bit value into a register                                                                                                                                                                                          |
| Loadf32        | loadf32 reg, imm                            | reg: dest register<br>imm: f32 immediate value                                                                                                                    | Loads a f32 value into a register                                                                                                                                                                                            |
| Loadf64        | loadf64 reg, imm                            | reg: dest register<br>imm: f64 immediate value                                                                                                                    | Loads a f64 value into a register                                                                                                                                                                                            |
| Copy           | copy dest, src                              | dest: dest register<br>src: src register                                                                                                                          | Copies a register's value into another register.<br>If this is an object type, then the object is duplicated                                                                                                                 |
| Clone          | clone dest, src                             | dest: dest register<br>src: src register                                                                                                                          | Copies a register's value into another register.<br>If this is an object type, then the reference count is incremented                                                                                                       |
| Move           | move dest, src                              | dest: destination register<br>src: source register                                                                                                                | Copies a register's value into another register.<br>If this is an object type, then the reference count is not incremented                                                                                                   |
| Clear          | clear reg                                   | reg: register to clear                                                                                                                                            | Zeros out a register. If this is an object type, then the reference count is decremented                                                                                                                                     |
| Destroy        | destroy reg                                 | reg: the object to be destroyed                                                                                                                                   | This will decrement the reference count, and if the reference count is 0 then the memory is completely freed and not reused. The register is also zeroed out                                                                 |
| Forget         | forget reg                                  | reg: the object to be forgotten                                                                                                                                   | Zeros out a register. If the register contains an object then the reference count is not decremented<br><br>This can leak memory so be careful.                                                                              |
| FetchReturn    | fetchret dest                               | dest: destination registers                                                                                                                                       | Fetches the value from the return slot. This will zero out the return slot                                                                                                                                                   |
| FetchRef       | fetchref dest, object                       | dest: destination register<br>object: a register with an object in it                                                                                             | Fetches the reference count of an object.<br>If the reference count is negative, then the reference count is atomic                                                                                                          |
| MakeShared     | mkshared object                             | object: register with an object in it                                                                                                                             | Makes an object become atomically reference counted. It also makes all children of the object also atomically reference counted.<br>This should be emitted on anything that is async or threaded                             |
| SetGlobal      | setglobal symbol, register                  | symbol: the symbol representing the global variable<br>register: the value to be placed in the global<br>                                                         | This will change ownership from the register to the global. This means that the register will get zeroed out and the reference count not updated.                                                                            |
| CopyGlobal     | copyglobal dest, symbol                     | dest: the destination register<br>symbol: the symbol representing the global variable                                                                             | Fetches a global variable from the symbol and places it into a register, duplicating the object if the global is one                                                                                                         |
| CloneGlobal    | cloneglobal dest, symbol                    | dest: the destination register<br>symbol: the symbol representing the global variable                                                                             | Fetches a global variable from the symbol into the register, incrementing the object if the global is one                                                                                                                    |
| Add{U,S,F}     | add{u,s,f} dest, left, right                | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point addition between two registers and store it in another register                                                                                                     |
| Sub{U,S,F}     | sub{u,s,f} dest, left, right                | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point subtraction between two registers and stores it in another register                                                                                                 |
| Mul{U,S,F}     | mul{u,s,f} dest, left, right                | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point multiplication between two registers and stores it in another register                                                                                              |
| Div{U,S,F}     | div{u,s,f} dest, left, right                | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point division between two registers and stores it in another register                                                                                                    |
| Rem{U,S}       | rem{u,s} dest, left, right                  | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned or **s**igned division between two registers and stores the remainder in another register                                                                                                              |
| And            | and dest, left, right                       | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs bitwise and between two registers and stores the result in another register                                                                                                                                         |
| Or             | or dest, left, right                        | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs bitwise or between two registers and stores the result in another register                                                                                                                                          |
| Xor            | xor dest, left, right                       | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs bitwise exclusive or between two registers and stores the result in another register                                                                                                                                |
| Not            | not dest, data                              | dest: destination register<br>data: data register                                                                                                                 | Performs bitwise not on a register and stores the result in another register                                                                                                                                                 |
| ShiftLeft      | shftl dest, left, right                     | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs bit shift left on a register and stores the result in another register                                                                                                                                              |
| LShiftRight    | lshftr dest, left, right                    | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs a logical bit shift right on a register and stores the result in another register                                                                                                                                   |
| AShiftRight    | ashftr dest, left, right                    | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs a arithmetic bit shift right on a register and stores the result in another register                                                                                                                                |
| ByteSwap       | bswap dest, src                             | dest: destination register<br>src: src register                                                                                                                   | Swaps the byte order of a register                                                                                                                                                                                           |
| Eq{I,F}        | eq{i,f} dest, left, right                   | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **i**nteger or **f**loating point equality between two registers and stores it in another register                                                                                                                  |
| Neq{I,F}       | neq{i,f} dest, left, right                  | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **i**nteger or **f**loating point inequality between two registers and stores it in another register                                                                                                                |
| Lt{U,S,F}      | lt{u,s,f} dest, left, right                 | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point less than comparison between two registers and stores it in another register                                                                                        |
| Gt{U,S,F}      | gt{u,s,f} dest, left, right                 | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point greater than comparison between two registers and stores it in another register                                                                                     |
| Lte{U,S,F}     | lte{u,s,f} dest, left, right                | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point less than or equal comparison between two registers and stores it in another register                                                                               |
| Gte{U,S,F}     | gte{u,s,f} dest, left, right                | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point greater than or equal comparison between two registers and stores it in another register                                                                            |
| CreateObject   | object dest, type_id, variant_id            | dest: destination register<br>type_id: immediate containing the type_id of the product type<br>variant_id: immediate holding which variant the sum type should be | Creates a new object type from a type id and a variant id                                                                                                                                                                    |
| IsNull         | null dest, object                           | dest: destination register<br>object: register holding object                                                                                                     | Checks if the object is null (0). This is for making the maybe type non-existant                                                                                                                                             |
| SetVariant     | setvariant object, variant_id               | object: the sum type to change the variant of<br>variant_id: the tag to change to                                                                                 | variant_id is statically known<br>object is register                                                                                                                                                                         |
| GetField       | getfield dest, object, index                | dest: destination register<br>object: register holding object<br>index: register to get the Nth member                                                            | Gets a field from an object, incrementing the reference count if applicable                                                                                                                                                  |
| CopyField      | copyfield dest, object, index               | dest: destination register<br>object: register holding object<br>index: register to get the Nth member                                                            | Copies a field from an object. For object types this will allocate a new object                                                                                                                                              |
| TakeField      | takefield dest, object, index               | dest: destination register<br>object: register holding object<br>index: register to get the Nth member                                                            | Removes a field from an object. The data in the field will then be placed in the destination register. This will not increment the reference count if the field is an object.<br><br>This can easily create use after frees. |
| SetField       | setfield object, value, index               | object: object with field being modified<br>value: value to put in object<br>index: register to set the Nth member                                                | Copies value and places it into the object's field, decrementing the reference count of the field before replacing.                                                                                                          |
| MoveField      | movefield object, value, index              | object: object with field being modified<br>value: value to put in object<br>index: register to set the Nth member                                                | Moves value and places it into the object's field, decrementing the reference count of the field before replacing. The reference count of the value is not incremented.<br><br>This can easily create use after frees.       |
| PlaceField     | placefield object, value, index             | object: object with field being modified<br>value: value to put in object<br>index: register to set the Nth member                                                | Moves value and places it into the object's field, decrementing the reference count of the field before replacing. The reference count of the value is incremented                                                           |
| Call           | call symbol, register...                    | symbol: a symbol representing the function<br>register...: a list of registers representing the function arguments                                                | Symbol is known statically, registers are copied into the stack frame of callee function starting at 0                                                                                                                       |
| CallTail       | calltail symbol, register...                | symbol: a symbol representing the function<br>register...: a list of registers representing the function arguments                                                | Symbol is known statically, registers are copied into the stack frame of callee function starting at 0.<br>For tail recursion<br>Logs the depth of the calls                                                                 |
| Invoke         | invoke closure, registers...                | closure: a register holding a closure<br>registers...: a list of registers representing the function arguments                                                    | Closure is an object type, arguments are copied in after existing variables in closure state                                                                                                                                 |
| InvokeTail     | invoketail registers...                     | closure: a register holding a closure<br>registers...: a list of registers representing the function arguments                                                    | Closure is an object type, arguments are copied in after existing variables in closure state.<br>Uses a special slot of the stackframe to ensure that closure's state remains consistent                                     |
| Return         | return register                             | register: a register holding the return value                                                                                                                     | Stops execution of the current function. Writes register to return slot of the previous frame.<br><br>If applicable, decrements the reference count of any objects in registers.                                             |
| ReturnTail     | returntail register                         | register: a register holding the return value                                                                                                                     | Stops execution of the current function. Writes register to return slot of the previous frame. This is for tail recursion<br><br>If applicable, decrements the reference count of any objects in registers.                  |
| ReturnUnit     | returnunit                                  | N/A                                                                                                                                                               | Stops execution of the current function. Does not write any data to the return slot of the previous frame<br><br>If applicable, decrements the reference count of any objects in registers.                                  |
| ReturnTailUnit | returntailunit                              | N/A                                                                                                                                                               | Stops execution of the current function. Does not write any data to the return slot of the previous frame. This is for tail recursion<br><br>If applicable, decrements the reference count of any objects in registers.      |
| CreateClosure  | closure symbol, dest                        | symbol: a symbol representing the function<br>dest: the destination for the created closure                                                                       | Symbol is known statically, the calling function's stack frame is copied into the closure.<br>We create the closure from the symbol                                                                                          |
| Jump           | jump blockid, offset                        | blockid: the block id to jump to<br>offset: a signed number indicating the offset to the next instruction                                                         | Offset is for use in the interpreter<br>Blockid is for use in the JIT compiler                                                                                                                                               |
| If             | if test (blockid, offset) (blockid, offset) | test: a register to test to take which jump<br>blockid: the blockid to jump to<br>offset: a signed number indicating the offset to the next instruction           | Test is either 0 for else or non-zero for then<br><br>Offset is for use in the interpreter<br>Blockid is for use in the JIT compiler                                                                                         |
| Switch         | switch value (value, blockid offset)...     | value: a register to switch on<br>(value, blockid, offset)...: branches to switch on if the values match                                                          | Switches on a value and jumps to a branch with the matching value                                                                                                                                                            |
| Match          | match object (variant_id, blockid, offset)  | object: the sum type to match on<br>(value, blockid, offset)...: branches to match on if the sum type matches variant                                             | Matches on object type variant and branch to the matching tag                                                                                                                                                                |
| StartBlock     | startblock blockid                          | blockid: starts the new block                                                                                                                                     | For use in the JIT compiler. This indicates that a new block is about to be filled. This should be bypassed by the Jump and If offsets to prevent a noop in the interpreter                                                  |

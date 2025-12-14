

# Circle File

A circle file is like Java's class file, it contains bytecode needed to be executed as well as typing data, strings, and ways to map bytecode to source code lines and columns

## File Format
All in pseudo Rust 


```Rust
#[repr(C)]
struct CircleFile {
	magic: u8, // 0
	major_ver: u8,
	minor_ver: u8,
	patch_ver: u8,
	module_name: StringIndex,
	product_types: [CircleProduct],
	sum_types: [CircleSum],
	functions: [CircleFunction],
	string_table: StringTable,
	bytecode_table: BytecodeTable,
	location_map: LocationMap,
}

#[repr(C)]
struct CircleProduct {
	name_index: StringIndex,
	type_name: StringIndex,
	members: [(StringIndex, TypeTag)],
}

#[repr(C)]
struct CircleSum {
	name_index: StringIndex,
	type_name: StringIndex,
	variants: [CircleVariant]
}

#[repr(C)]
struct CircleVariant {
	name_index: StringIndex,
	members: [(StringIndex, TypeTag)],
}

#[repr(C)]
struct CircleFunction {
	visibility: CircleVisibility,
	name_index: StringIndex,
	type_name: StringIndex,
	return_type: TypeTag,
	arg_types: [TypeTag],
	bytecode_index: BytecodeIndex,
	variables: u32,
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
	Object,
}

```


## Bytecode

| Instruction    | Form                           | Arguments                                                                                                                                                         | Explanation                                                                                                                                                                                                             |
| -------------- | ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Load8          | load8 reg, imm                 | reg: dest register<br>imm: immediate value                                                                                                                        | Loads a byte into a registers                                                                                                                                                                                           |
| Load16         | load16 reg, imm                | reg: dest register<br>imm: immediate value                                                                                                                        | Loads a 16bit value into a register                                                                                                                                                                                     |
| Load32         | load32 reg, imm                | reg: dest register<br>imm: immediate value                                                                                                                        | Loads a 32bit value into a register                                                                                                                                                                                     |
| Load64         | load64 reg, imm                | reg: dest register<br>imm: immediate value                                                                                                                        | Loads a 64bit value into a register                                                                                                                                                                                     |
| Loadf32        | loadf32 reg, imm               | reg: dest register<br>imm: f32 immediate value                                                                                                                    | Loads a f32 value into a register                                                                                                                                                                                       |
| Loadf64        | loadf64 reg, imm               | reg: dest register<br>imm: f64 immediate value                                                                                                                    | Loads a f64 value into a register                                                                                                                                                                                       |
| Copy           | copy dest, src                 | dest: dest register<br>src: src register                                                                                                                          | Copies a register's value into another register.<br>If this is an object type, then the object is duplicated                                                                                                            |
| Move           | move dest, src                 | dest: destination register<br>src: source register                                                                                                                | Copies a register's value into another register.<br>If this is an object type, then the reference count is incremented                                                                                                  |
| Clear          | clear reg                      | reg: register to clear                                                                                                                                            | Zeros out a register. If this is an object type, then the reference count is decremented                                                                                                                                |
| FetchReturn    | fetchret dest                  | dest: destination registers                                                                                                                                       | Fetches the value from the return slot. This will zero out the return slot                                                                                                                                              |
| FetchRef       | fetchref dest, object          | dest: destination register<br>object: a register with an object in it                                                                                             | Fetches the reference count of an object.<br>If the reference count is negative, then the reference count is atomic                                                                                                     |
| MakeShared     | mkshared object                | object: register with an object in it                                                                                                                             | Makes an object become atomically reference counted. It also makes all children of the object also atomically reference counted.<br>This should be emitted on anything that is async or threaded                        |
| Add{U,S,F}     | add{u,s,f} dest, left, right   | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point addition between two registers and store it in another register                                                                                                |
| Sub{U,S,F}     | sub{u,s,f} dest, left, right   | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point subtraction between two registers and stores it in another register                                                                                            |
| Mul{U,S,F}     | mul{u,s,f} dest, left, right   | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point multiplication between two registers and stores it in another register                                                                                         |
| Div{U,S,F}     | div{u,s,f} dest, left, right   | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point division between two registers and stores it in another register                                                                                               |
| Rem{U,S}       | rem{u,s} dest, left, right     | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned or **s**igned division between two registers and stores the remainder in another register                                                                                                         |
| And            | and dest, left, right          | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs bitwise and between two registers and stores the result in another register                                                                                                                                    |
| Or             | or dest, left, right           | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs bitwise or between two registers and stores the result in another register                                                                                                                                     |
| Xor            | xor dest, left, right          | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs bitwise exclusive or between two registers and stores the result in another register                                                                                                                           |
| Not            | not dest, data                 | dest: destination register<br>data: data register                                                                                                                 | Performs bitwise not on a register and stores the result in another register                                                                                                                                            |
| ShiftLeft      | shftl dest, left, right        | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs bit shift left on a register and stores the result in another register                                                                                                                                         |
| LShiftRight    | lshftr dest, left, right       | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs a logical bit shift right on a register and stores the result in another register                                                                                                                              |
| AShiftRight    | ashftr dest, left, right       | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs a arithmetic bit shift right on a register and stores the result in another register                                                                                                                           |
| ByteSwap       | bswap dest, src                | dest: destination register<br>src: src register                                                                                                                   | Swaps the byte order of a register                                                                                                                                                                                      |
| Eq{I,F}        | eq{i,f} dest, left, right      | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **i**nteger or **f**loating point equality between two registers and stores it in another register                                                                                                             |
| Neq{I,F}       | neq{i,f} dest, left, right     | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **i**nteger or **f**loating point inequality between two registers and stores it in another register                                                                                                           |
| Lt{U,S,F}      | lt{u,s,f} dest, left, right    | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point less than comparison between two registers and stores it in another register                                                                                   |
| Gt{U,S,F}      | gt{u,s,f} dest, left, right    | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point greater than comparison between two registers and stores it in another register                                                                                |
| Lte{U,S,F}     | lte{u,s,f} dest, left, right   | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point less than or equal comparison between two registers and stores it in another register                                                                          |
| Gte{U,S,F}     | gte{u,s,f} dest, left, right   | dest: destination register<br>left: lefthand side register<br>right: righthand side register                                                                      | Performs **u**nsigned, **s**igned, or **f**loating point greater than or equal comparison between two registers and stores it in another register                                                                       |
| CreateProduct  | product dest, type_id          | dest: destination register<br>type_id: immediate containing the type_id of the product type                                                                       | Creates a new product type from a type id                                                                                                                                                                               |
| CreateSum      | sum dest, type_id, variant_id  | dest: destination register<br>type_id: immediate containing the type_id of the product type<br>variant_id: immediate holding which variant the sum type should be | Creates a new sum type from a type id and a variant id                                                                                                                                                                  |
| IsNull         | null dest, object              | dest: destination register<br>object: register holding object                                                                                                     | Checks if the object is null (0). This is for making the maybe type non-existant                                                                                                                                        |
| GetField       | getfield dest, object, index   | dest: destination register<br>object: register holding object<br>index: register to get the Nth member                                                            | Gets a field from an object, incrementing the reference count if applicable                                                                                                                                             |
| CopyField      | copyfield dest, object, index  | dest: destination register<br>object: register holding object<br>index: register to get the Nth member                                                            | Copies a field from an object. For object types this will allocate a new object                                                                                                                                         |
| SetField       | setfield object, value, index  | object: object with field being modified<br>value: value to put in object<br>index: register to set the Nth member                                                | Copies value and places it into the object's field, decrementing the reference count of the field before replacing.                                                                                                     |
| MoveField      | movefield object, value, index | object: object with field being modified<br>value: value to put in object<br>index: register to set the Nth member                                                | Moves value and places it into the object's field, decrementing the reference count of the field before replacing. The reference count of the value is incremented                                                      |
| Call           | call symbol, register...       | symbol: a symbol representing the function<br>register...: a list of registers representing the function arguments                                                | Symbol is known statically, registers are copied into the stack frame of callee function starting at 0                                                                                                                  |
| CallTail       | calltail symbol, register...   | symbol: a symbol representing the function<br>register...: a list of registers representing the function arguments                                                | Symbol is known statically, registers are copied into the stack frame of callee function starting at 0.<br>For tail recursion<br>Logs the depth of the calls                                                            |
| Invoke         | invoke closure, registers...   | closure: a register holding a closure<br>registers...: a list of registers representing the function arguments                                                    | Closure is an object type, arguments are copied in after existing variables in closure state                                                                                                                            |
| InvokeTail     | invoketail registers...        | closure: a register holding a closure<br>registers...: a list of registers representing the function arguments                                                    | Closure is an object type, arguments are copied in after existing variables in closure state.<br>Uses a special slot of the stackframe to ensure that closure's state remains consistent                                |
| Return         | return register                | register: a register holding the return value                                                                                                                     | Stops execution of the current function. Writes register to return slot of the previous frame.<br><br>If applicable, decrements the reference count of any objects in registers.                                        |
| ReturnTail     | returntail register            | register: a register holding the return value                                                                                                                     | Stops execution of the current function. Writes register to return slot of the previous frame. This is for tail recursion<br><br>If applicable, decrements the reference count of any objects in registers.             |
| ReturnUnit     | returnunit                     | N/A                                                                                                                                                               | Stops execution of the current function. Does not write any data to the return slot of the previous frame<br><br>If applicable, decrements the reference count of any objects in registers.                             |
| ReturnTailUnit | returntailunit                 | N/A                                                                                                                                                               | Stops execution of the current function. Does not write any data to the return slot of the previous frame. This is for tail recursion<br><br>If applicable, decrements the reference count of any objects in registers. |
| CreateClosure  | closure symbol, dest           | symbol: a symbol representing the function<br>dest: the destination for the created closure                                                                       | Symbol is known statically, the calling function's stack frame is copied into the closure.<br>We create the closure from the symbol                                                                                     |

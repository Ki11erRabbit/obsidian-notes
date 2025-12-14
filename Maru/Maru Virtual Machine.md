

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
	bytecode_index: BytecodeIndex
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

| Instruction | Form            | Arguments                                  | Explanation                         |
| ----------- | --------------- | ------------------------------------------ | ----------------------------------- |
| Load8       | load8 reg, imm  | reg: dest register<br>imm: immediate value | Loads a byte into a registers       |
| Load16      | load16 reg, imm | reg: dest register<br>imm: immediate value | Loads a 16bit value into a register |
| Load32      | load32 reg, imm | reg: dest register<br>imm: immediate value | Loads a 32bit value into a register |
| Load64      | load64 reg, imm | reg: dest register<br>imm: immediate value | Loads a 64bit value into a register |
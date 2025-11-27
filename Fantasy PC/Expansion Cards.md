An expansion card is simply a way for the native operating system or other libraries to interact with the virtual machine to provide additional functionality that the core runtime does not provide.

There needs to be a way for cards to provide an arbitrary way to interact with the "processor." The card provides the api that the programer must follow to interact with it. This provides maximum flexibility as new cards can be created whenever they are needed.


# C API

Cards are shared libraries that implement the `fantapc_register` function. This function takes in a struct pointer that is used to provide the callbacks for interacting with the state of the shared library. It also returns the mode that the card is capable with interacting with.

```C
typedef struct card {
	size_t (*name)(uint8_t *name);
	void (*call)(registers_t *regs)
} fantapc_card_t;

typedef enum mode {
	FANTAPC_16;
	FANTAPC_32;
	FANTAPC_63;
} fantapc_mode_t;

fantapc_mode_t fantapc_register(fantapc_card_t *card);

```

# Internal API

## Machine Config File
This is a toml file that specifies how the virtual machine will be configured. This specifies the CPU mode, the RAM amount, and the cards to load. The cards can be specified in one of 3 locations.
1. Load path
2. Current Directory
3. Relative/Absolute path

### Example File
```
[fantapc]
mode = 16
ram = 2048

[card0]
name = "vt100"

[card1]
name = "sound"

[card2]
name = "joypad" 

```
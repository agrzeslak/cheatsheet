---
layout: default
title: Buffer Overflow
tags:
    - binary exploitation
    - binary ninja
    - evan's debugger (edb)
    - gnu debugger (gdb)
    - hopper
    - ida
    - immunity debugger
    - ollydbg
    - peda
---
# Buffer Overflow
## Registers
- `ESP` Extended Stack Pointer - points to top of running processes' stack, for pushing/poping values to/from stack
- `EIP` Extended Instruction Pointer - current address location for the instruction being executed (controls path of code execution)

## Tools
Unique buffer string
```shell
pattern_create.rb <length of string (bytes)>  # Generate unique string
pattern_offset.rb <hex string to match>       # Locate offset of hex value within string
```

Convert instructions to op codes
```shell
nasm_shell.rb
```

## Bad Characters
Common bad characters:
- `00` null byte (terminates strings and hence truncates payload)
- `0D` carraige return (can terminate fields such as password inputs where return terminates it)

## Concepts
### ASLR
- Address Space Layout Randomisation
- Randomly offsets the location of modules and in-memory structures
- Aims to prevent exploits from reliably locating sections of code
- Can be bypassed if the location of certain memory regions (especially DLL mappings) can be predicted, controlled or discovered
- Absence of DEP can allow the use of heap spraying to place code in predictable locations

### DEP
- Data Execution Prevention
- Prevents areas of memory such as the stack from being executed
- In most cases is not robust enough without ASLR

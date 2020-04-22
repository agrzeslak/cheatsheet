---
layout: default
title: Windows
parent: Privilege Escalation
---

# Windows Privilege Escalation
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

Setting up a lower priv user for authenticated assessments
```shell
net user [username] [password] /add     # Create new user
net localgroup [group] [username] /add  # Add to desired group, may need a localgroup to allow logon
runas /user:[username] powershell.exe   # Run PowerShell as user
net user [username] /delete             # Delete when finished
```
## Automated Checks
### PowerUp
PowerUp.ps1.b64
```powershell
powershell.exe -nop -exec bypass
IEX([System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String((Get-Content -Path .\PowerUp.ps1.b64))))
Invoke-AllChecks -ErrorAction SilentlyContinue | Out-File -Encoding ASCII powerup.log
```

---

## Manual Checks
### OS
```powershell
systeminfo
hostname
net users            # Users
net user <username>  # User information
```

### Networking
```powershell
ipconfig /all              # Network interfaces
route print                # Routing table
arp -A                     # ARP cache for all interfaces
netstat -ano               # Network connections; -anob if admin
netsh firewall show state  # Firewall rules
netsh firewall show config
```

### Applications
```powershell
schtasks /query /fo LIST /v  # Scheduled tasks
tasklist /SVC                # Running processes and services
net start                    # Windows services
DRIVERQUERY                  # Drivers
```

### File system
Unquoted service paths
```powershell
wmic service get name,displayname,pathname,startmode |findstr /i “auto” |findstr /i /v “c:\windows\\” |findstr /i /v “””  # CMD
wmic service get name,displayname,pathname,startmode |findstr /i 'auto' |findstr /i /v 'c:\windows\' |findstr /i /v '\"'  # PowerShell
```

Search for string (e.g. passwords - case insensitive)
```powershell
Get-ChildItem -Path <path> -Recurse -Force -ErrorAction SilentlyContinue | Select-String -Pattern <pattern> | Select path,filename,linenumber,line
```
- Select-String can:
    - `-Include` files as a comma separated list, e.g. `'*.txt','*.csv'`
    - `-Excluse` files as a comma separated list, e.g. `'*.exe','*.dll','*.msi','*.java','*.jar'`; useful for ignoring binaries

Search filesystem (case insensitive)
```powershell
Get-ChildItem -Path <path> -Recurse -Force -ErrorAction SilentlyContinue -Include <search>  # -File to only search for files
```

Access control lists (permissions)
```powershell
Get-Acl | Format-List  # -Path <path> for directory other than current
icacls <path>
```

| Type | Abbreviation | Full |
|--- | --- | --- |
| Inheritance Settings | OI | Object Inherit |
| | CI | Container Inherit |
| | IO | Inherit Only |
| | NP | Don't propagate inherit |
| | I | Permission inherited from parent container |
| Basic Access Permissions | D | Delete |
| | F | Full |
| | N | No |
| | M | Modify |
| | RX | Read and Execute |
| | R | Read-only |
| | W | Write-only |
| Detailed Permissions | DE | Delete |
| | RC | Read Control |
| | WDAC | Write DAC |
| | WO | Write Owner |
| | S | Synchronise |
| | AS | Access System Security|
| | MA | Maximum Allowed Permissions |
| | GR | Generic Read |
| | GW | Generic Write |
| | GE | Generic Execute |
| | GA | Generic All |
| | RD | Read Data/List Directory |
| | WD | Write Data/Add File |
| | AD | Append Data/Add Subdirectory |
| | REA | Read Extended Attributes |
| | WEA | Write Extended Attributes |
| | X | Execute/Traverse |
| | DC | Delete Child |
| | RA | Read Attributes |
| | WA | Write Attributes |

```powershell
forfiles /P directory /S /D +<dd/mm/yyyy>  # Files modified since date (date /t or echo %date% to check date format)
Dir <root directory> -r | ? {! $_.PSIsContainer -AND $_.lastwritetime -ge '<dd/mm/yy>'} 
```

### WMIC
[FuzzySecurity .bat script for extracting relevant info using WMIC](http://www.fuzzysecurity.com/tutorials/files/wmic_info.rar)

System Patches
```powershell
wmic qfe get Caption,Description,HotFixID,InstalledOn
```

### Helpful links
- <https://www.fuzzysecurity.com/tutorials/16.html>
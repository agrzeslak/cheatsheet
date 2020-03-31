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
runas /user:[username] powershell.exe   # Run powershell as user
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

General details
```powershell
systeminfo
```

```powershell
hostname
```

Our user and their permissions
```powershell
echo %username%
```

```powershell
net users
```

```powershell
net user [USERNAME]
```

### Networking
Network interfaces
```powershell
ipconfig /all
```

Routing table
```powershell
route print
```

ARP cache for all interfaces
```powershell
arp -A
```

Network connections
```powershell
netstat -ano  # -anob if admin
```

Firewall rules
```powershell
netsh firewall show state
```

```powershell
netsh firewall show config
```

### Applications
Scheduled tasks
```powershell
schtasks /query /fo LIST /v
```

Running processes and services
```powershell
tasklist /SVC
```

Windows services
```powershell
net start
```

Drivers can have vulns
```powershell
DRIVERQUERY
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

Files modified in last _n_ days
```powershell
forfiles /P directory /S /D +(today'date - [DAYS] days)
```

Files modified since date
```powershell
forfiles /P directory /S /D +dd/mm/yyyy
```

```powershell
Dir C:\ -r | ? {! $_.PSIsContainer -AND $_.lastwritetime -ge 'dd/mm/yy'} 
```
- check date format on machine; you might be dealing with Americans

### WMIC
[FuzzySecurity .bat script for extracting relevant info using WMIC](http://www.fuzzysecurity.com/tutorials/files/wmic_info.rar)

System Patches
```powershell
wmic qfe get Caption,Description,HotFixID,InstalledOn
```

### Helpful links
- <https://www.fuzzysecurity.com/tutorials/16.html>
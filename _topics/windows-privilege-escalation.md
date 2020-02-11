---
layout: default
title: Windows Privilege Escalation
tags:
    - administrator
    - microsoft
    - nt\system
    - privesc
---
# Windows Privilege Escalation
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

Search for string (e.g. passwords)
```powershell
Get-ChildItem -Path <path> -Recurse -Force -ErrorAction SilentlyContinue | Select-String -Pattern <pattern> | Select path,linenumber,line
```
- Select-String can:
    - `-Include` files as a comma separated list, e.g. `'*.txt','*.csv'`
    - `-Excluse` files as a comma separated list, e.g. `'*.exe','*.dll','*.msi','*.java','*.jar'`; useful for ignoring binaries

Search filesystem
```powershell
Get-ChildItem -Path <path> -Recurse -Force -ErrorAction SilentlyContinue -Include <search>  # -File to only search for files
```

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
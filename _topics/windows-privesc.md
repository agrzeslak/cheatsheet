---
layout: default
title: Windows Privesc
tags:
    - administrator
    - escalation
    - microsoft
    - nt
    - privilege
    - system
---
# Windows Privesc
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
netstat -ano
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
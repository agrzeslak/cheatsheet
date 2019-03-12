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
```ps
systeminfo
```

```ps
hostname
```

Our user and their permissions
```ps
echo %username%
```

```ps
net users
```

```ps
net user [USERNAME]
```

### Networking
Network interfaces
```ps
ipconfig /all
```

Routing table
```ps
route print
```

ARP cache for all interfaces
```ps
arp -A
```

Network connections
```ps
netstat -ano
```

Firewall rules
```ps
netsh firewall show state
```

```ps
netsh firewall show config
```

### Applications
Scheduled tasks
```ps
schtasks /query /fo LIST /v
```

Running processes and services
```ps
tasklist /SVC
```

Windows services
```ps
net start
```

Drivers can have vulns
```ps
DRIVERQUERY
```

### File system
Files modified in last _n_ days
```ps
forfiles /P directory /S /D +(today'date - [DAYS] days)
```

Files modified since date
```ps
forfiles /P directory /S /D +dd/mm/yyyy
```

```ps
Dir C:\ -r | ? {! $_.PSIsContainer -AND $_.lastwritetime -ge 'dd/mm/yy'} 
```
- check date format on machine; you might be dealing with Americans

### WMIC
[FuzzySecurity .bat script for extracting relevant info using WMIC](http://www.fuzzysecurity.com/tutorials/files/wmic_info.rar)

System Patches
```ps
wmic qfe get Caption,Description,HotFixID,InstalledOn
```

### Helpful links
- <https://www.fuzzysecurity.com/tutorials/16.html>
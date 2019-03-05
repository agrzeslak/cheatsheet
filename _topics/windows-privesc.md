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

### Helpful links
- <https://www.fuzzysecurity.com/tutorials/16.html>
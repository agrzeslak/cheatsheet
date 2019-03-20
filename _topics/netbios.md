---
layout: default
title: NetBIOS
tags:
    - 139
    - network basic input output system
---
# NetBIOS
## Information Gathering
```shell
nbtscan 10.10.10.125
```

```powershell
nbtstat -a 10.10.10.10
```

## Nmap scripts
Retrieve target's NetBIOS names and MAC address
```shell
nmap -p 139 -vv --script nbstat 10.10.10.10
```

CVE-2012-1182 root RCE < 3.6.3 (safe)
```shell
nmap -p 139 -vv --script samba-vuln-cve-2012-1182 10.10.10.10
```

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
nbtscan <ip or ip range>
```

## Nmap scripts
Retrieve target's NetBIOS names and MAC address
```shell
nmap -p 139 -vv --script nbstat <ip>
```

CVE-2012-1182 root RCE < 3.6.3 (safe)
```shell
nmap -p 139 -vv --script samba-vuln-cve-2012-1182 <ip>
```

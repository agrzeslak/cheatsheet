---
layout: default
title: Samba
tags:
    - 139
---
# Samba
### nmap scripts
CVE-2012-1182 root RCE < 3.6.3 (safe)

```shell
nmap -p 139 -vv --script samba-vuln-cve-2012-1182 10.10.10.10
```

---
layout: default
nav-order: 1
title: NetBIOS
parent: Network Services
---

# NetBIOS
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Enumeration
```shell
nbtscan <ip or ip range>
```

---

## Nmap scripts
Retrieve target's NetBIOS names and MAC address
```shell
nmap -p 139 -vv --script nbstat <ip>
```

CVE-2012-1182 root RCE < 3.6.3 (safe)
```shell
nmap -p 139 -vv --script samba-vuln-cve-2012-1182 <ip>
```

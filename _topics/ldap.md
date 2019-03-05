---
layout: default
title: LDAP
tags:
    - 389
    - access
    - directory
    - enumeration
    - lightweight
    - protocol
---
# LDAP

Dump info (anonymous bind)

```shell
nmap -p 389 --script ldap-search -Pn 10.10.10.10
```

_or_

```shell
ldapsearch -x -h 10.10.10.10 -s base namingcontexts
```
- use result to fill in dc values below

```shell
ldapsearch -x -h 10.10.10.10 -s sub -b 'dc=<dc>,dc=<dc>'
```
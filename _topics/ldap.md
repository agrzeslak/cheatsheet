---
layout: default
title: ldap
tags:
    - 389
    - access
    - directory
    - enumeration
    - lightweight
    - protocol
---
# ldap

Dump info (anonymous bind)

```shell
nmap -p 389 --script ldap-search -Pn 10.10.10.10
```

_or_

```shell
ldapsearch -x -h 10.10.10.10 -s base namingcontexts
```

```shell
ldapsearch -x -h 10.10.10.10 -s sub -b 'dc=<dc>,dc=<dc>'
```
- dc taken from above result
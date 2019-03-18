---
layout: default
title: LDAP
tags:
    - 389
    - enumeration
    - injection
    - lightweight directory access protocol
---
# LDAP
## Enumeration

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

## Injection

todo
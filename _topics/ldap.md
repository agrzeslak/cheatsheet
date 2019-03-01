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

Dump info (`-x` for anonymous bind)

`nmap -p 389 --script ldap-search -Pn 10.10.10.10`

_or_

`ldapsearch -x -h 10.10.10.10 -s base namingcontexts`

`ldapsearch -x -h 10.10.10.10 -s sub -b 'dc=<dc>,dc=<dc>'` _dc taken from above result_
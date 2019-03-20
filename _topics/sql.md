---
layout: default
title: SQL
tags:
    - injection
    - SQLi
    - structured query language
---
# SQL
## MSSQL
Impacket CLI Client
```shell
/usr/share/doc/python-impacket/examples/mssqlclient.py [USER]@10.10.10.10
```
- `[[domain/]username[:password]@]10.10.10.10`
- If you provide the password as part of the target then it's treated as an ldap query; simply use the prompt otherwise

---
layout: default
title: SQL
tags:
    - injection
    - SQLi
    - structured query language
---
# SQL
## CLI Tools
```shell
sqsh -S 10.10.10.10 -U [USERNAME] -P [PASSWORD/HASH]
```
- Supports Kerberos but don't know of way to use Windows authentication

## MSSQL
Impacket CLI Client
```shell
/usr/share/doc/python-impacket/examples/mssqlclient.py [USER]@10.10.10.10
```
- `[[domain/]username[:password]@]10.10.10.10`
- If you provide the password as part of the target then it's treated as an LDAP query; use the prompt otherwise

Test `xp_cmdshell`
```sql
xp_cmdshell 'whoami'
GO
```

If disabled, attempt to enable
```sql
EXEC SP_CONFIGURE 'show advanced options', 1  -- only do this if it doesn't work regularly and it's actually an advanced option
RECONFIGURE  -- as above
EXEC SP_CONFIGURE 'xp_cmdshell', 1
RECONFIGURE
GO
```

List databases
```sql
SELECT name FROM master.sys.databases
```

List tables in database
```sql
USE [DATABASE]
SELECT * FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_TYPE='BASE TABLE'
```

## PowerUpSQL
_Must run from Windows; issues with Powershell for Linux_

<https://github.com/NetSPI/PowerUpSQL>

todo
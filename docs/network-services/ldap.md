---
layout: default
title: LDAP
parent: Network Services
---

# LDAP
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

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

---

## Injection

TODO
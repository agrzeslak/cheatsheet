---
layout: default
title: SNMP
parent: Network Services
---

# SNMP
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Enumeration
### Querying information
```shell
snmp-check [-c <community string>] [-v <version>] <ip>
snmpwalk -v<version> -c <community string> <ip> [SMIv2 identifier]  # With no SMIv2 identifier, will pull whole tree
```
- Common community strings include `public`, `private` and `manager`

| Detail | SMIv2 Identifier |
| --- | --- |
| User accounts | `1.3.6.1.4.1.77.1.2.25` |
| System processes | `1.3.6.1.2.1.25.1.6.0` |
| Running programs | `1.3.6.1.2.1.25.4.2.1.2` |
| Process paths | `1.3.6.1.2.1.25.4.2.1.4` |
| Storage units | `1.3.6.1.2.1.25.2.3.1.4` |
| Hostname | `1.3.6.1.2.1.1.5` |
| Share information | `1.3.6.1.4.1.77.1.2.3.1.1` |
| Share information | `1.3.6.1.4.1.77.1.2.27` |
| TCP ports | `1.3.6.1.2.1.6.13.1.3` |
| Software names | `1.3.6.1.2.1.25.6.3.1.2` |

### Brute-forcing community strings
```shell
onesixtyone -c <dictionary> <ip>  # /usr/share/doc/onesixtyone/dict.txt is default dictionary (bruteforce community strings)
```
- SecLists also has a list of community strings at `Discovery/SNMP/common-snmp-community-strings.txt`

---

## How does SNMP work?
- SNMP exposes management details of a host stored in the MIB (Management Information Base)
- The MIB is structured as a tree, with the leaves storing relevant information
- Different locations are expressed using a subset of Abstract Syntax Notation One (ASN.1) called [Structure of Management Information Version 2 (SMIv2)](https://tools.ietf.org/html/rfc2578)
    - The common `1.3.6.1` prefix corresponds to `iso > org > dod > internet`, see [here](https://www.iana.org/assignments/smi-numbers/smi-numbers.xhtml)
    - Can explore the tree online <https://oidref.com/>, e.g. <https://oidref.com/1.3.6.1.2.1.25.1.6> for system processes
- Community strings are similar to user IDs or passwords and are sent along with an SNMP Get-Request to allow access for querying details

---

## Versions
1. Only requires plaintext community string to set up, does not support 64-bit counters, only 32
2. Has support for 64-bit counters and a party-based security system which was viewed as overly complex and not widely adopted
- 2c: Subset of 2; has a community-based security model instead of party
- 2u: Subset of 2; has a user-based security model (like v3) instead of party
3. Adds security to 64-bit counters - encryption and authentication
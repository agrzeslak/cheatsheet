---
layout: default
title: SNMP
tags:
    - 161
    - simple network management protocol
    - UDP
---
# SNMP
## Enumeration
snmp-check
```shell
snmp-check <ip>
```
Community Strings
```shell
snmpwalk -v1 -c public <ip>
snmpwalk -v1 -c public <ip> 1.3.6.1.4.1.77.1.2.25     # User accounts
snmpwalk -v1 -c public <ip> 1.3.6.1.2.1.25.1.6.0      # System processes
snmpwalk -v1 -c public <ip> 1.3.6.1.2.1.25.4.2.1.2    # Running programs
snmpwalk -v1 -c public <ip> 1.3.6.1.2.1.25.4.2.1.4    # Process paths
snmpwalk -v1 -c public <ip> 1.3.6.1.2.1.25.2.3.1.4    # Storage units
snmpwalk -v1 -c public <ip> 1.3.6.1.2.1.1.5           # Hostname
snmpwalk -v1 -c public <ip> 1.3.6.1.4.1.77.1.2.3.1.1  # Share information
snmpwalk -v1 -c public <ip> 1.3.6.1.4.1.77.1.2.27     # Share information
snmpwalk -v1 -c public <ip> 1.3.6.1.2.1.6.13.1.3      # TCP ports
snmpwalk -v1 -c public <ip> 1.3.6.1.2.1.25.6.3.1.2    # Software names
onesixtyone -c <dictionary> <ip>  # /usr/share/doc/onesixtyone/dict.txt is default dictionary
snmp-check <ip>
```

## How does SNMP work?
- SNMP exposes management details of a host stored in the MIB (Management Information Base)
- The MIB is structured as a tree, with the leaves storing relevant information
- Different locations are expressed using a subset of Abstract Syntax Notation One (ASN.1) called [Structure of Management Information Version 2 (SMIv2)](https://tools.ietf.org/html/rfc2578)
    - The common `1.3.6.1` prefix corresponds to `iso > org > dod > internet`, see [here](https://www.iana.org/assignments/smi-numbers/smi-numbers.xhtml)
    - Can explore the tree online <https://oidref.com/>, e.g. <https://oidref.com/1.3.6.1.2.1.25.1.6> for system processes
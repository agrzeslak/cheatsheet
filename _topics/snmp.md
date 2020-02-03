---
layout: default
title: SNMP
tags:
    - 161
    - simple network management protocol
    - UDP
---
# SNMP
snmp-check
```shell
snmp-check <ip>
```
Community Strings
```shell
snmpwalk -v1 -c public <ip>
snmpwalk -v1 -c public <ip> 1.3.6.1.4.1.77.1.2.25     # Windows user accounts
snmpwalk -v1 -c public <ip> 1.3.6.1.2.1.25.4.2.1.2    # Windows running programs
snmpwalk -v1 -c public <ip> .1.3.6.1.2.1.1.5          # Windows hostname
snmpwalk -v1 -c public <ip> 1.3.6.1.4.1.77.1.2.3.1.1  # Windows share information
snmpwalk -v1 -c public <ip> 1.3.6.1.4.1.77.1.2.27     # Windows share information
snmpwalk -v1 -c public <ip> 1.3.6.1.2.1.6.13.1.3      # Windows TCP ports
snmpwalk -v1 -c public <ip> 1.3.6.1.2.1.25.6.3.1.2    # Software name
onesixtyone -c <dictionary> <ip>  # /usr/share/doc/onesixtyone/dict.txt is default dictionary
snmp-check <ip>
```
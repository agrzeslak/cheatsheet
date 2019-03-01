---
layout: default
title: nmap
tags:
    - discovery
    - enumeration
    - network
    - ports
    - scanning
    - tcp 
    - udp
---
# nmap
### Host Discovery
No port scan

`nmap -sn -oA nmap 10.10.10.10/24`

ARP scan

`nmap -PR -oA nmap 10.10.10.10/24`

_can also use **netdiscover**_
### Scanning a Known Host

TCP scan

`nmap -sV -sC -vv -oA nmap 10.10.10.10`

UDP scan

`nmap -sU -sC -vv -oA nmap 10.10.10.10`

Aggressive TCP scan

`nmap -A -vv -oA nmap 10.10.10.10`

Where `-A` is the equivalent of:
- `-O`: OS detection
- `-sV`: version scanning
- `-sC`: default script scanning
- `--traceroute`

Additional options:
- `-p-`: full portscan (1-65535)
- `-T4`: faster scan
- `-sT`: TCP connect scan (for when default `-sS` SYN scan is unavailable; e.g. lack of root permissions)
- `-sF`: FIN scan (can be used to bypass firewalls that filter the SYN packet)
- `-sP`: Ping scan (use ICMP packets)

Scripts

`nmap -p <port> --script <script name> -Pn 10.10.10.10`

`ls -al /usr/share/nmap/scripts`

Result | Meaning
:--- | :---
open | An application is actively accepting connections on this port
closed | Port is accessible, but nothing is listening
filtered | Port is inaccessible; packets are being filtered by a firewall, etc.
unfiltered | Port is accessible, but not known whether open or closed (only ACK scan returns this)
open\|filtered | Unable to determine whether open or filtered
closed\|filtered | Unable to determine whether closed or filtered
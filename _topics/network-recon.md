---
layout: default
title: Network Recon
tags:
    - discovery
    - enumeration
    - netdiscover
    - network
    - nmap
    - ports
    - scanning
    - tcp 
    - udp
    - unicornscan
---
# Network Recon
## Host Discovery
No port scan (TCP SYN to 443, TCP ACK to 80 and ICMP)
```shell
nmap -sn -oA nmap 10.10.10.10/24
```

ARP scan (for discovery on same LAN)
```shell
netdiscover -i eth0 -r 10.10.10.10/24
```

```shell
nmap -PR -oA nmap 10.10.10.10/24
```

## Port Scanning

TCP scan
```shell
nmap -sV -sC -vv -p- -oA full-tcp 10.10.10.10
```

UDP scan
```shell
nmap -sU -sC -vv -oA udp 10.10.10.10
```

```shell
unicornscan -m U -Iv -l full-udp 10.10.10.10:a
```
- all ports with `:a`

Aggressive TCP scan
```shell
nmap -A --reason -vv -p- -oA full-tcp 10.10.10.10
```

Full TCP and UDP
```shell
masscan -e tun0 -p 0-65535,U:0-65535 --interactive -oL masscan 10.10.10.10
```
- default `--rate` is 100; if the network/target permits, consider higher values for substantially faster scans
- follow up with nmap, e.g. `nmap -sT -sU -A -p 22,139,445 -oA nmap 10.10.10.10`

Where `-A` is the equivalent of:
- `-O`: OS detection
- `-sV`: version scanning
- `-sC`: default script scanning
- `--traceroute`

Additional options:
- `-p-`: full portscan (1-65535)
- `-T4`: faster scan (`-TN` where `N` is the timing mode; default is `T3`)
- `-sT`: TCP connect scan (for when default `-sS` SYN scan is unavailable; e.g. lack of root permissions - also ensures the connection doesn't stay open and have DOS potential)
- `-sF`: FIN scan (can be used to bypass firewalls that filter the SYN packet)
- `-sP`: Ping scan (use ICMP packets)

Scripts

```shell
nmap -p [PORT] --script [SCRIPT NAME] -Pn 10.10.10.10
```

```shell
ls -al /usr/share/nmap/scripts
```

Result | Meaning
:--- | :---
open | An application is actively accepting connections on this port
closed | Port is accessible, but nothing is listening
filtered | Port is inaccessible; packets are being filtered by a firewall, etc.
unfiltered | Port is accessible, but not known whether open or closed (only ACK scan returns this)
open\|filtered | Unable to determine whether open or filtered
closed\|filtered | Unable to determine whether closed or filtered

---
layout: default
title: Network Recon
----

# Network Recon
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Host Discovery
No port scan (ICMP echo request, TCP SYN to 443, TCP ACK to 80 and ICMP timestamp request)
```shell
nmap -sn -oA <file name> <ip range>
```

Find hosts with open port
```shell
nmap -p <ports> -oA <file name> --open <ip range> 
```

ARP scan (for discovery on same LAN)
```shell
netdiscover -i eth0 -r <ip range>
nmap -PR -oA nmap <ip range>
```

---

## Port Scanning
Full TCP scan
```shell
nmap -sV -sC -vv -p- -oA full-tcp <ip>
nmap -A --reason -vv -p- -oA full-tcp <ip>  # Aggresive
```

UDP scan
```shell
nmap -sU -sC -vv -oA udp <ip>
unicornscan -m U -Iv -l full-udp <ip>:a  # :a for all ports
```

Full TCP and UDP
```shell
masscan -e tun0 -p 0-65535,U:0-65535 --interactive -oL masscan <ip>
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
nmap -p <port> --script <script> -Pn 10.10.10.10
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

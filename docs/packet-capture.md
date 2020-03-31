---
layout: default
title: Packet Capture
tags:
    - tcpdump
    - wireshark
---

# Packet Capture
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## tcpdump
### General usage
```shell
tcpdump -i <interface> <filter>
tcpdump -i <interface> -l <filter> | etc...               # Line buffered mode to process line by line for piping (-C is packet buffered)
tcpdump -i <interface> -W <file> -G <seconds> -C <>       # Rotating output files
```
- `<filter>` such as `tcp`, `udp`, `port 80`, `icmp`, `host 8.8.8.8`, `&&/and`, `||/or`, `!/not` etc.
- `-w <file>` writes to file
- `-n` will not resolve hostnames, `-nn` will not resolve hostnames or ports, useful to speed up large captures
- `-s<n>` snap length, size of packet to capture, `-s0` is unlimited, needed to pull binaries/files
- `-A` outputs only ascii text

### Rotating output files
```shell
tcpdump -i <interface> -w <file> [-C <max file size (bytes)>] [-G <seconds>] [-W <num max files>] <filter>
```
- `-C` file size in bytes (millions - 1,000,000 not 1,048,576) before opening new file for writing (first file has name specified by `-w`, following files have iterator starting at 1)
- `-G` seconds before file specified by `-w` is rotated
    - `-w` should include a time format as defined by strftime to include time (e.g. `%Y-%m-%d %H:%M:%S`); if not specified, file will be overwritten each time
    - If used with `-C`, filenames take the form of `file<count>`
- `-W`
    - Used with `-C`, limits number of files created, creating a rotating buffer, naming them with leading '0's to support max num files and allow them to sort
    - Used with `-G`, limits number of dump files, exiting with 0 status when finished
    - Used with `-C` and `-G`, results in a rotation of files per timeslice

---

## Wireshark
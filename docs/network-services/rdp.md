---
layout: default
title: RDP
parent: Network Services
---

# RDP
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Brute-Forcing Credentials
```shell
ncrack -v [options] rdp://<ip>,<service options>
```
- `-U <usernames file>`
- `-P <passwords file>`
- `--user <usernames comma-separated list>`
- `--pass <passwords comma-separated list>`
- `-p <port>`
- `-f` quit after match found
- Service options:
    - `cl=<min connections>`
    - `CL=<max connections>`
    - `at=<auth attempts per connection>`
    - `cd=<connection delay between each connection initiation>`
    - `cr=<max num of service connection attempts>`
    - `to=<timeout>` overall, not per connection
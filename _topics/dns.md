---
layout: default
title: DNS
tags:
    - 53
    - domain name service
    - TCP
    - UDP
    - zone transfer
---
# DNS
Zone transfer
```shell
dig axfr @[DNS SERVER]
```

```shell
dig axfr [DOMAIN] @[DNS SERVER]
```

Zone transfer + other checks
```shell
fierce -dns [DOMAIN] -dnsserver [DNS SERVER]
```
- Tools such as dnsenum, dnsrecon, etc. can all achieve the same goal

## DNS on TCP?
- Used when response is >512 bytes
- Indicative of several possibilities:
    - DNS zone transfers
    - DNSSEC
    - IPv6
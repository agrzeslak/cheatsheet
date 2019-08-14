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
dig axfr <domain> @<dns server>
```

Zone transfer + other checks
```shell
fierce -dns <domain> -dnsserver <dns server>
```
- Tools such as dnsenum, dnsrecon, etc. can all achieve the same goal

## DNS on TCP?
- Used when response is >512 bytes
- Indicative of several possibilities:
    - DNS zone transfers
    - DNSSEC
    - IPv6
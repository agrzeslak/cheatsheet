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
dnsrecon -d <domain> -t axfr
```

Zone transfer + other checks
```shell
dnsenum <domain>
fierce -dns <domain> -dnsserver <dns server>
```

## DNS on TCP?
- Used when response is >512 bytes
- Indicative of several possibilities:
    - DNS zone transfers
    - DNSSEC
    - IPv6
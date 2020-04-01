---
layout: default
title: DNS
parent: Network Services
---

# DNS
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Enumeration
Zone Transfer
```shell
dig axfr <domain> @<dns server>
dnsrecon -d <domain> -t axfr
```

Zone transfer + other checks
```shell
dnsenum <domain>
fierce -dns <domain> -dnsserver <dns server>
```

---

## DNS on TCP?
- Used when response is >512 bytes
- Indicative of several possibilities:
    - DNS zone transfers
    - DNSSEC
    - IPv6
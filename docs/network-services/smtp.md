---
layout: default
title: SMTP
parent: Network Services
---

# SMTP
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Connecting and Enumerating
```shell
nc -nv <ip> <port>
```
- Commands when connected:
    - `VRFY <username>` provides details about user, including whether exists (250 status exists, 550 if not)
    - `EXPN <mailing list>` get membership of mailing list (a username counts as a mailing list)
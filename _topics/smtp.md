---
layout: default
title: SMTP
tags:
    - 25
    - simple mail transfer protocol
---
# SMTP
## Connecting
```shell
nc -nv <ip> <port>
```
- Commands when connected:
    - `VRFY <username>` provides details about user, including whether exists (250 status exists, 550 if not)
    - `EXPN <mailing list>` get membership of mailing list (a username counts as a mailing list)
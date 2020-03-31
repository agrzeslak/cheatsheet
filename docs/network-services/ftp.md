---
layout: default
nav-order: 1
title: FTP
parent: Network Services
---

# FTP
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Recursively download all files
```shell
wget -m ftp://username:password@10.10.10.10/
```
- `--no-passive-ftp` to use active FTP
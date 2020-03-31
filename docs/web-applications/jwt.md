---
layout: default
title: JWT
parent: Web Applications
---
# JWT
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## None Algorithm
Change the `alg` within the header to `none`/`None` (based on implementation)
```json
{
    "alg": "none",
    "typ": "JWT"
}
```
- Try both with and without signature

## HS256 Brute Force
Test whether a weak secret has been used
```shell
hashcat -m 16500 <jwt> /usr/share/wordlists/rockyou.txt
```

## RSA/HMAC Alg Confusion
- If the server is expecting RSA (e.g. `"alg": "RS256"`), try `"alg": "HS256"` and sign with the public key
- Some old libraries/poor implementations will assume the public key is the HMAC secret and verify the spoofed token

## Misc Tips
- JWTs can be identified by the pattern `eyJXXX.eyJXXX.XXXXX`
- JWTs are URL-safe base64 encoded without padding:
    - `+` → `-`
    - `/` → `_`
    - Trailing `-`s (padding) removed
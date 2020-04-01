---
layout: default
title: Passwords/Hashes
---

# Passwords/Hashes
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Creating Wordlists
### Password Profiling
```shell
cewl <url> [-m <min length>] -w <output file>                        # Scrape contents of website
john --wordlist=<cewl output file> --rules --stdout > <output file>  # Mutate passwords
cat <base wordlist> >> <our new wordlist>                            # Can include a regular wordlist
```
- Custom `john` rules can be defined within `/etc/john/john.conf`
    - E.g. `$[0-9]$[0-9]` appends 2 numbers to the end of each password

### Whole Key Space
```shell
crunch <min-len> <max-len> [charset] [-o <file>]                                          # Charset string
crunch <min-len> <max-len> -f /usr/share/crunch/charset.lst <charset option> [-o <file>]  # Charset file
```
- `charset` must have characters in order of lower -> upper -> numbers -> symbols
- The `charset options` for `-f` are within `/usr/share/crunch/charset.lst` such as `mixalpha`

```shell
crunch <min-len> <max-len> -t <pattern> [-o <file>]  # Generate using pattern
```
- `@` lower case
- `,` upper case
- `%` numbers
- `^` symbols
- `@test@` will only replace the `@` symbols

---

## Cracking Hashes
John the Ripper
```shell
john <hash file>  # Brute-force with auto-detection
```

## Pass-the-Hash
Tools
```shell
export SMBHASH=<NTLM>
```
```shell
pth-curl
pth-net
pth-rpcclient
pth-smbclient
pth-smbget
pth-sqsh
pth-winexe -U <username>% //<ip> <command>
pth-wmic
pth-wmis
```
- `pth-<tab>` for list on Kali
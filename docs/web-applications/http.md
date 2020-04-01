---
layout: default
title: HTTP(S)
parent: Web Applications
---

# HTTP(S)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Gobuster
```shell
gobuster dir -u <url> -w <wordlist> -o <output file>
```
- `-a <user-agent>`
- `-c <cookies>`
- `-s <positive status codes>` default "`200,204,301,302,307,403`"
- `-t <threads>`
- `-x <extensions>` e.g. "`.txt,.php`"

Generating a custom wordlist
```shell
cewl <url> -w original.cewl
john --wordlist=original.cewl --rules --stdout > transformed.cewl
cat /usr/share/SecLists/Discovery/Web_Content/common.txt >> transformed.cewl
```

Other wordlists:
- `/usr/share/dirb/wordlists/common.txt`
- `/usr/share/dirbuster/wordlists/directory-list-2.3-small.txt`
- `/usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt`

## Nikto
```shell
nikto -h <ip>
```

## wfuzz
GET request
```shell
wfuzz -c -w <wordlist> -w <wordlist> <url>/login?user=FUZZ&pass=FUZ2Z
```

POST request

```shell
wfuzz -c -w <wordlist> -w <wordlist> -d "login=FUZZ&pwd=FUZ2Z" <url>
```

Additional options:
- `-b`: specify cookie(s)
- `-H`: add/replace header
- `--oF <filename>`: output to file
- `-X`: HTTP method
- `-z`: specify payload (`-w` is an alias for `-z file,<wordlist>`)
- `--hc/hl/hw/hh`: hide responses with specified code/lines/words/chars
- `--sc/sl/sw/sh`: show responses with specified code/lines/words/chars
- `--ss/hs`: show/hide responses with specified regex

## wpscan
```shell
wpscan -u <ip>/wp/
```
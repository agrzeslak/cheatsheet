---
layout: default
title: HTTP(S)
tags:
    - 80
    - 443
    - curl
    - gobuster
    - https
    - nikto
    - wfuzz
    - wordpress
    - wpscan
---
# HTTP(S)
### Gobuster
```shell
gobuster -u 10.10.10.10 -w /usr/share/SecLists/Discovery/Web_Content/common.txt -o gobuster
```

Additional options:
- `-a`: User-Agent
- `-s`: positive status codes (default "`200,204,301,302,307,403`")
- `-t`: threads
- `-x`: file extension(s) to search for e.g. "`.txt,.php`"

[recursive-gobuster](https://github.com/epi052/recursive-gobuster) wrapper by epi
```shell
./recursive-gobuster.pyz -w /usr/share/SecLists/Discovery/Web_Content/common.txt -x html,php 10.10.10.10
```

Generating a custom wordlist
```shell
cewl http://10.10.10.10 -w original.cewl
john --wordlist=original.cewl --rules --stdout > transformed.cewl
cat /usr/share/SecLists/Discovery/Web_Content/common.txt >> transformed.cewl
```

Some other wordlists:
- `/usr/share/dirb/wordlists/common.txt`
- `/usr/share/dirbuster/wordlists/directory-list-2.3-small.txt`
- `/usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt`

### Nikto
```shell
nikto -h 10.10.10.10
```

### wfuzz
GET request

```shell
wfuzz -c -w users.txt -w pass.txt https://10.10.10.10/login?user=FUZZ&pass=FUZ2Z
```

POST request

```shell
wfuzz -c -w users.txt -w pass.txt -d "login=FUZZ&pwd=FUZ2Z" https://10.10.10.10/
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

### wpscan

```shell
wpscan -u 10.10.10.10/wp/
```
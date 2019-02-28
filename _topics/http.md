---
layout: default
title: http
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
# http(s)
### Curl

### Gobuster

Standard

`gobuster -u 10.10.10.10 -w /usr/share/SecLists/Discovery/Web_Content/common.txt -o gobuster`

Some other wordlists:
- `/usr/share/dirb/wordlists/common.txt`
- `/usr/share/dirbuster/wordlists/directory-list-2.3-small.txt`
- `/usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt`

Additional Options:
- `-a`: User-Agent
- `-s`: positive status codes (default "`200,204,301,302,307,403`")
- `-t`: threads
- `-x`: file extension(s) to search for e.g. "`.txt,.php`"

### Nikto

`nikto -h 10.10.10.10`

### wfuzz

`wfuzz -c -z file,users.txt -z file,pass.txt https://10.10.10.10/login?user=FUZZ&pass=FUZ2Z`

### wpscan

`wpscan -u 10.10.10.10/wp/`
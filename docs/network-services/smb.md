---
layout: default
nav-order: 1
title: SMB
parent: Network Services
---

# SMB
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Exposed Shares
```shell
smbmap -H <ip>
smbmap -H <ip> -u <username>  # adding anything as the <username> will sometimes return results
enum4linux -v <ip>            # wrapper around tools such as rpcclient that gives lots of info
rpcclient -U <username> <ip>  # "" <username> for null sessions, empty password when prompted
```
- Commands while connected via `rpcclient`:
    - `srvinfo` gives server details
    - `enumdomuser` gives configured users
    - `getdompwinfo` gives domain password policy

---

## Nmap CVE Scans
Safe vulnerability scan
```shell
nmap -p 445 -sT -Pn --script=smb-vuln-ms06-025,smb-vuln-ms07-029,smb-vuln-ms08-067,smb-vuln-ms10-061,smb-vuln-ms17-010,smb-vuln-cve-2017-7494 <ip>
```

Windows 2000 regsvc null pointer crash (_**service crashes if successful**_)
```shell
nmap -p 445 -sT -Pn --script=smb-vuln-regsvc-dos <ip>
```

CVE-2009-3103 DOS check (Windows Vista and some of 7 - _**BSOD if successful**_)
```shell
nmap -p 445 -sT -Pn --script=smb-vuln-cve2009-3103 <ip>
```

CVE-2010-2550 memory corruption check (_**BSOD if successful**_)
```shell
nmap -p 445 -sT -Pn --script=smb-vuln-ms10-054 <ip>
```

---

## Pass-the-hash
List directories
```shell
smbclient -U <user>%<hash> --pw-nt-hash -L <ip>
```
```shell
smbmap -u <user> -p '<NTLM hash>:<LM hash>' -H <ip>
```
- _LM hash is legacy and can usually be anything_
- `--download <path>`: download file
- `-R`: recursively list files

Open specific directory

```shell
smbclient -U <user>%<hash> --pw-nt-hash //<ip>/<directory>
```

---

## Regular usage
Mount
```shell
mount -t cifs -o username=<username> //<ip>/ /mnt/smb/
```

---

## Local Checks
What is exposed?
```powershell
net share          # List shares
net share <share>  # Info about share
```
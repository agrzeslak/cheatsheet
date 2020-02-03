---
layout: default
title: Active Directory
tags:
    - 88
    - 445
    - 464
    - 543
    - 544
    - 749
    - 750
    - 751
    - 752
    - 753
    - 754
    - 760
    - 1109
    - 3268
    - kerberoasting
    - kerberos
    - TCP
    - UDP
    - windows

---
# Active Directory

```powershell
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()                               # Current domain
([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()  # Domain trusts
[System.DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest()                               # Current forest
([System.DirectoryServices.ActiveDirectory.Forest]::GetForest((New-Object System.DirectoryServices.ActiveDirectory.DirectoryContext('Forest', 'forest-of-interest.local')))).GetAllTrustRelationships()  # Forest trust relationships
nltest /dclist:<domain>                                                                             # DCs of a domain
net group "domain controllers" /domain                                                              # DCs of a domain
nltest /dsgetdc:<domain>                                                                            # DC for currently authenticated session
nltest /domain_trusts                                                                               # Domain trusts from cmd shell
nltest /user:"<user>"                                                                               # User info
set l                                                                                               # DC for currently authenticated session
klist                                                                                               # Domain name and DC the user authenticated to
klist sessions                                                                                      # All logon sessions, including NTLM authenticated sessions
klist                                                                                               # Kerberos tickets for the session
klist tgt                                                                                           # Cached krbtgt
set u                                                                                               # whoami on older Windows systems
```
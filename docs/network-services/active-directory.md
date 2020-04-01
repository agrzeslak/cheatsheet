---
layout: default
title: Active Directory
parent: Network Services
---

# Active Directory
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

```powershell
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()                               # Current domain
([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()  # Domain trusts
[System.DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest()                               # Current forest
# Forest trust relationships
([System.DirectoryServices.ActiveDirectory.Forest]::GetForest((New-Object System.DirectoryServices.ActiveDirectory.DirectoryContext('Forest', 'forest-of-interest.local')))).GetAllTrustRelationships()

nltest /dclist:<domain>                 # DCs of a domain
net group "domain controllers" /domain  # DCs of a domain
nltest /dsgetdc:<domain>                # DC for currently authenticated session
nltest /domain_trusts                   # Domain trusts from cmd shell
nltest /user:"<user>"                   # User info
set l                                   # DC for currently authenticated session
klist                                   # Domain name and DC the user authenticated to
klist sessions                          # All logon sessions, including NTLM authenticated sessions
klist                                   # Kerberos tickets for the session
klist tgt                               # Cached krbtgt
set u                                   # whoami on older Windows systems
```
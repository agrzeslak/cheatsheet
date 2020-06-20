---
layout: default
title: Windows (Methodology)
parent: Privilege Escalation
---

# Windows Privilege Escalation Methodology
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

- Is the OS patched?
- What groups/permissions does our user belong to/have?
- Do we have SeImpersonatePrivilege (Hot/Rotten potato)?
    - Hot/Rotten Potato
    - Named pipes
- Network services
    - Are additional network services available locally (bound to localhost, firewall, etc.)?
    - Are additional network services available on other hosts (IP whitelisting, firewalls, etc.)?
- Are there any custom services/software installed?
    - What does it do? Can we leverage it?
    - Is it patched? Are there known vulnerabilities?
    - Does our user have special permissions for that software?
- Services
    - Are there DDLs referenced which are missing, called with abusable unquoted paths or we can modify ([useful link](https://hacknpentest.com/windows-privilege-escalation-dll-hijacking/))?
    - Are there abusable unquoted service paths?
    - Are there modifiable registry keys referenced?
    - Are there modifiable executable files?
    - Do we have the `SERVICE_CHANGE_CONFIG` permission to modify the `binPath`?
- Registry
    - Are there any modifiable programs configured to autorun?
    - Is `AlwaysInstallElevated` enabled?
- Scheduled Tasks
    - Are there any overwritable files?
    - Are there any missing files?
- Are there any modifiable startup applications?
- What files do we have access to? Anything interesting/non-standard?
- Passwords
    - Are passwords stored in the registry?
    - Are passwords stored in files (configs, logs, etc.)?
    - Is there a cached SAM file?
    - Are there passwords in memory (procdump, Mimikittenz)?

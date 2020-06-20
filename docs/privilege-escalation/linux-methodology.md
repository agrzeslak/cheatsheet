---
layout: default
title: Linux (Methodology)
parent: Privilege Escalation
---

# Linux Privilege Escalation Methodology
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

- Is the Kernel patched?
- Network Services
    - Are additional network services available locally (bound to localhost, firewalls, etc.)?
    - Are additional network services available on other hosts (IP whitelisting, firewalls, etc.)?
- What groups does our user belong to?
- Is there any custom software installed?
    - What does it do? Can we leverage it?
    - Is it patched? Are there known vulnerabilities?
    - Does our user have special permissions for that software?
- What files do we have access to? Anything interesting/non-standard?
- Sudo
    - Shell Escape Sequences
    - Abuse Intended Functionality
    - `LD_PRELOAD`/`LD_LIBRARY_PATH`
- Cron jobs
    - Path
    - Wildcards
    - File overwrite
- File permissions
    - SUID binaries
        - Shared Object Injection
        - Symlink
        - Environment Variables
    - Startup Scripts
    - Configuration Files
- NFS
- Daemons
- Passwords
    - History
    - Config files
    - Logs
    - Memory ???

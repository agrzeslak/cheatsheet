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
    - Is `LD_PRELOAD`/`LD_LIBRARY_PATH` enabled?
    - Is a static, writable `RPATH` defined?
- Cron jobs
    - Has the cron config redefined the `$PATH` env var to be abusable?
    - Is globbing used such that we can have our own files take preference over the intended files?
    - Can we overwrite files used for cron jobs?
- File permissions
    - SUID binaries
        - Are there missing/writable Shared Objects which we can add/replace?
        - Are there references to other files/symlinks which we can add/replace?
        - Are environment variables (namely `$PATH`) used in an abusable manner (can we make our payload take precendence/overwrite)?
    - Are there any abusable startup scripts (writable/replacable)?
    - Are there any configuration files which are writable/addable or misconfigured?
- Are there NFS shares with root squashing disabled
- Are Daemons appropriately patched?
- Are there passwords stored in
    - History files such as `~/.bash_history`?
    - Config files?
    - Logs?
    - Memory?

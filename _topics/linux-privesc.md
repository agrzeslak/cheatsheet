---
layout: default
title: Linux Privesc
tags:
    - escalation
    - openbsd
    - priviliege
    - root
    - unix
---
# Linux Privesc
### Upgrading Shells
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

### Enumeration Scripts
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

### OS
Distribution details
```shell
cat /proc/version
```

```shell
cat /etc/issue
```

Kernel
```shell
uname -a
```

### Users
Who are we?
```shell
id
```

Legacy password management?
```shell
cat /etc/passwd
```

Shadow access?
```shell
cat /etc/shadow
```

Groups
```shell
cat /etc/group
```

Sudo permissions
```shell
sudo -l
```

```shell
cat /etc/sudoers
```

Environment variables
```shell
env
```

History
```shell
cat ~/.bash_history
```

```shell
cat ~/.*_history
```

SSH keys
```shell
ls -alR ~/.ssh/
```

```shell
cat ~/.ssh/*
```

```shell
ls -alR /etc/ssh/
```

```shell
cat /etc/ssh/*
```

Who else is logged in?
```shell
w
```

```shell
who
```

Last logged in users
```shell
last
```

### Applications
Running services
```shell
ps -ef  # standard syntax
```

```shell
ps aux  # BSD syntax
```

Installed applications
```shell
ls -al /usr/bin
```

```shell
ls -al /sbin
```

```shell
dpkg -l
```

```shell
rpm -qa
```

```shell
ls -al /var/cache/apt/archives
```

```shell
ls -al /var/cache/yum
```

### File system
Check home directories
```shell
ls -alR /root/
```

```shell
ls -alR /home/
```

Configs
```shell
find /etc/ -iname *.conf -exec ls -al {} \;
```

```shell
find /opt/ -iname *.conf -exec ls -al {} \;
```

Cron jobs
```shell
crontab -l
```

```shell
ls -al /etc/cron* 
```

```shell
cat /etc/cron* 
```

SUID/SGID
```shell
find / -perm -g=s -type f 2>/dev/null
```

```shell
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
```
- writable

```shell
find / -executable -type d 2>/dev/null
```
- executable

```shell
find / -writable -executable -type d 2>/dev/null
```
- writable and executable

What files are being accessed?
```shell
lsof -i
```

```shell
lsof -i :<port>
```
- `-i` can take `[46][protocol][@hostname|hostaddr][:service|port]` (where `[46]` determines IPv4/6)

Log files
```shell
find /etc/ -type f -iname "*log" 2>/dev/null
```

```shell
find /var/ -type f -iname "*log" 2>/dev/null
```

```shell
ls -al /var/log/
```

Look for passwords in file system (takes ages)
```shell
grep -iR pass /
```

Mounted file systems
```shell
mount
```

```shell
df
```

Unmounted file systems
```shell
cat /etc/fstab
```

### Networking
NIC(s) and other networks
```shell
ifconfig -a
```

```shell
cat /etc/network/interfaces
```

```shell
cat /etc/sysconfig/network
```

```shell
cat /etc/networks
```

Network connections
```shell
netstat -antup
```

DNS
```shell
cat /etc/resolv.conf
```

```shell
dnsdomainname
```

```shell
hostname
```

Packet filtering and NAT
```shell
iptables -L
```

Routing
```shell
route
```

Communications with other users and hosts
```shell
lsof -i
```

Packet sniffing
```shell
tcpdump -i <interface>
```

```shell
tcpdump tcp dst <ip> <port>
```

ARP cache
```shell
arp
```

### Kernel exploits
[Dirty Cow](https://dirtycow.ninja/)
- < 4.8.0-26.28 for Ubuntu 16.10
- < 4.4.0-45.66 for Ubuntu 16.04 LTS
- < 3.13.0-100.147 for Ubuntu 14.04 LTS
- < 3.2.0-113.155 for Ubuntu 12.04 LTS
- < 3.16.36-1+deb8u2 for Debian 8
- < 3.2.82-1 for Debian 7
- < 4.7.8-1 for Debian unstable 4.8

### Useful links
- <https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/>
- <https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/#method3upgradingfromnetcatwithmagic>
- <https://blog.pentests.pl>
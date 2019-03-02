---
layout: default
title: linux privesc
tags:
    - escalation
    - openbsd
    - priviliege
    - root
    - unix
---
# linux privesc
### Upgrading shells
`python -c 'import pty;pty.spawn("/bin/bash")'`

`echo os.system('/bin/bash')`

`/bin/sh -i`

### Enumeration Scripts
[LinEnum.sh](https://github.com/rebootuser/LinEnum) `./LinEnum.sh -r report -e /tmp/ -t`

- `-s`: supply current user password to check permissions
- `-k <keyword>`: search .conf and .log files for keyword

[LinuxPrivChecker.py](https://github.com/sleventyeleven/linuxprivchecker) `python linuxprivchecker.py`

### OS
Distribution details

`cat /proc/version`

`cat /etc/issue`

Kernel

`uname -a`

### Users
Who are we?

`id`

Legacy password management?

`cat /etc/passwd`

Shadow access?

`cat /etc/shadow`

Groups

`cat /etc/group`

Sudo permissions

`sudo -l`

`cat /etc/sudoers`

Environment variables

`env`

History

`cat ~/.bash_history`

`cat ~/.*_history`

SSH keys

`ls -alR ~/.ssh/`

`cat ~/.ssh/*`

`ls -alR /etc/ssh/`

`cat /etc/ssh/*`

Who else is logged in?

`w`

`who`

Last logged in users

`last`

### Applications
Running services

`ps -ef` (standard syntax)

`ps aux` (BSD syntax)

Installed applications

`ls -al /usr/bin`

`ls -al /sbin`

`dpkg -l`

`rpm -qa`

`ls -al /var/cache/apt/archives`

`ls -al /var/cache/yum`

### File system
Check home directories

`ls -alR /root/`

`ls -alR /home/`

Configs

`find / -iname "/etc/*.conf" -exec ls -al {} \;`

`find / -iname "/opt/*.conf" -exec ls -al {} \;`

Cron jobs

`crontab -l`

`ls -al /etc/cron* `

`cat /etc/cron* `

SUID/SGID

`find / -perm -g=s -type f 2>/dev/null`

`find / -perm -u=s -type f 2>/dev/null`

Readable files in /etc/

`find /etc/ -readable -type f 2>/dev/null`

Writable files in /etc/ - check if can alter settings

`find /etc/ -writable -type f 2>/dev/null`

Writable/executable directories (/tmp, /var/tmp and /dev/shm are standard)

`find / -writable -type d 2>/dev/null` _writable_

`find / -executable -type d 2>/dev/null` _executable_

`find / -writable -executable -type d 2>/dev/null` _writable and executable_

What files are being accessed?

`lsof -i`

`lsof -i :<port>`

Log files

`find /etc/ -type f -iname "*log" 2>/dev/null`

`find /var/ -type f -iname "*log" 2>/dev/null`

`ls -al /var/log/`

Look for passwords in file system (takes ages)

`grep -iR pass /`

Mounted file systems

`mount`

`df`

Unmounted file systems

`cat /etc/fstab`

### Networking
NIC(s) and other networks

`ifconfig -a`

`cat /etc/network/interfaces`

`cat /etc/sysconfig/network`

`cat /etc/networks`

What's exposed?

`netstat -antup`

DNS

`cat /etc/resolv.conf`

`dnsdomainname`

`hostname`

Packet filtering and NAT

`iptables -L`

Routing

`route`

Communications with other users and hosts

`lsof -i`

Packet sniffing

`tcpdump -i <interface>`

`tcpdump tcp dst <ip> <port>`

ARP cache

`arp`

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


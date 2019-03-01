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
Enumeration scripts:

[LinEnum.sh](https://github.com/rebootuser/LinEnum) `./LinEnum.sh -r report -e /tmp/ -t`
- `-s`: supply current user password to check permissions
- `-k <keyword>`: search .conf and .log files for keyword

[LinuxPrivChecker.py](https://github.com/sleventyeleven/linuxprivchecker) `python linuxprivchecker.py`

Distribution details

`uname -a`

`cat /proc/version`

`cat /etc/issue`

Environment variables

`env`

History

`cat ~/.bash_history`

Running services

`ps -ef` (standard syntax)

`ps aux` (BSD syntax)

`top`

`cat /etc/services`

Installed applications

`ls -al /usr/bin`

`ls -al /sbin`

`dpkg -l`

`rpm -qa`

`ls -al /var/cache/apt/archives`

`ls -al /var/cache/yum`

Configs

`find / -iname "/etc/*.conf" -exec ls -al {} \;`

`find / -iname "/opt/*.conf" -exec ls -al {} \;`

Cron jobs

`crontab -l`

`ls -al /var/spool/cron `

`ls -al /etc/ | grep cron `

`ls -al /etc/cron* `

`cat /etc/cron* `

`cat /etc/at.allow `

`cat /etc/at.deny `

`cat /etc/cron.allow `

`cat /etc/cron.deny `

`cat /etc/crontab `

`cat /etc/anacrontab `

`cat /var/spool/cron/crontabs/root`


Search for passwords (takes ages)

`grep -iR pass /`

Useful links:
- <https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/>


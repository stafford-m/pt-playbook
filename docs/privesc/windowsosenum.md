# Windows Enumeration

User info
```powershell
whoami
whoami /groups
whoami /priv # sudo -l

net user
Get-LocalUser
```

Group info
```powershell
net localgroup $group
Get-LocalGroup
Get-LocalGroupMember $group
```

Network info
```powershell
ipconfig /all
route print
netstat -ano
```

Software and update info
```powershell
systeminfo
Get-Process
Get-CimInstance -Class win32_quickfixengineering | Where-Object { $_.Description -eq "Security Update" }
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
reg query HKLM\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall /s /v DisplayName
```

Powershell info
```powershell
Get-History
(Get-PSReadlineOption).HistorySavePath
C:\Users\Public\Transcripts\
```

Smb and files
```powershell
net view \\dc01 /all
Get-ChildItem -Path C:\Users\dave\ -Include *.txt,*.pdf,*.xls,*.xlsx,*.doc,*.docx -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\xampp -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue
Get-ChildItem -Path C:\ -Include *.kdbx, *.txt, *.docx -File -Recurse -ErrorAction SilentlyContinue
```
# AD Enumeration

## Powerview

```powershell
Import-Module .\PowerView.ps1
```

User enumeration
```powershell
Get-NetDomain
Get-NetUser
Get-NetUser | select cn
```

Group Enumeration
```powershell
Get-NetGroup | select cn
Get-NetGroup "Sales Departmnet" | select member
```

Computer Enumeration
```bash
Get-NetComputer | select operatingsystem,operatingsystemversion,dnshostname
```

find accounts that have admin on local computer
```powershell
Find-LocalAdminAccess
```

enumerate active sessions
```powershell
Get-NetSession -ComputerName files04 -Verbose # enumerate active sessions
```

Cool version
```powershell
.\PsLoggedon.exe \\files04
```

review regkey acls
```powershell
Get-Acl -Path HKLM:SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity\ | fl
```

## Service Principals

windows default run against servers and clients on domain
```powershell
setspn -L iis_service
```

Powerview version
```powershell
Get-NetUser -SPN | select samaccountname,serviceprincipalname
```

## Object Permissions

Enumerating
```powershell
Get-ObjectAcl -Identity jason
```

Convert sids faster
```powershell
$sid, $sid, $sid | Convert-SidToName
```

Enumerate GenericAll permissions
```powershell
Get-ObjectAcl -Identity "IT" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights 
```

add member to group
```powershell
net group "IT" jason /add /domain
```

Find ALL domain shares
```powershell
Find-DomainShare
```

## Bloodhound

Import for use
```powershell
Import-Module .\Sharphound.ps1
```
Run collection
```powershell
Invoke-BloodHound -CollectionMethod All -OutputDirectory $path -OutputPrefix "audit_one"
```

## Extra

```bash
gpp-decrypt "+bsY0V3d4/KgX3VJdO/vyepPfAN1zMFTiQDApgR92JE" # cpasswords vuln
```
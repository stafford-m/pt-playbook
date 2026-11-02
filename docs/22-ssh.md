# 22 - SSH

## Service Usage

Connect with a username
```bash
ssh $un@$ip
```
Connect with a private key
```bash
ssh -i keyfile $un@$ip
```
Connect on a non-standard port
```bash
ssh -p 2222 $un@$ip
```
Run a single remote command
```bash
ssh $un@$ip 'id'
```

## 0. Fast Path (try these first)

Test credentials you already have
```bash
ssh $un@$ip 'id'
```
Fix private key permissions
```bash
chmod 600 keyfile
```
Connect with the private key
```bash
ssh -i keyfile $un@$ip
```

## 1. Banner Grab & Unauthenticated Enumeration

Version and auth methods
```bash
nmap -p 22 -sV -sC $ip
```
Manual banner grab
```bash
nc -nv $ip 22
```
Timing-based username enumeration (only if relevant)
```bash
ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null $un@$ip 2>&1 | head
```

## 2. Authenticated Enumeration

Enumerate current user and system quickly
```bash
id; uname -a; cat /etc/passwd; sudo -l; find / -perm -4000 2>/dev/null
```
Read authorized_keys
```bash
cat ~/.ssh/authorized_keys
```
List ssh directory contents
```bash
ls -la ~/.ssh/
```

## 3. Password Spraying / Bruteforcing

SSH brute is slow and noisy — spray only, and only after other methods fail.
Spray a single password across users
```bash
hydra -L users.txt -p 'Spring2026!' -t 4 ssh://$ip
```
Brute force with a password list
```bash
hydra -L users.txt -P pass.txt -t 4 ssh://$ip
```

## 5. Quick Wins

### 5.1 Key reuse (the big SSH win on OSCP)
```bash
# If you found a private key on another box/service, try it here
chmod 600 found_key
ssh -i found_key $un@$ip
```

### 5.2 Add your own key if you have write to authorized_keys
```bash
ssh-keygen -t rsa -f mykey -N ''
cat mykey.pub >> /target/user/.ssh/authorized_keys   # from an existing foothold
ssh -i mykey $un@$ip
```

### 5.3 Port forward for internal pivot
```bash
ssh -L 8080:$internal_ip:80 $un@$ip      # local forward
ssh -D 1080 $un@$ip                       # SOCKS proxy
```

### 5.4 Escaping restricted shells (if landed in rbash)
```bash
ssh $un@$ip -t "bash --noprofile"
ssh $un@$ip "sh"
```

## 6. Config Paths

sshd
```text
/etc/ssh/sshd_config
/etc/ssh/ssh_config
```
User keys
```text
~/.ssh/id_rsa
~/.ssh/authorized_keys
/home/*/.ssh/
```

Config quick-reference:
```text
sshd_config -> PermitRootLogin, PasswordAuthentication, AuthorizedKeysFile, AllowUsers
authorized_keys -> whose keys can log in as whom
```

## 7. Post-Exploit / Pivot

- Shell → PrivEsc (see Linux PrivEsc notes)
- Local port forward/SOCKS → reach internal-only services
- Stolen keys → lateral movement to other boxes/users

### Common gotchas
```text
- "Permission denied (publickey)" = trying to use key but no key/password auth. Check which auth methods offered.
- Key perms too open? ssh ignores them — chmod 600 first.
- Strict host key warnings on repeat? Use -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null.
- SSH brute = last resort; it's the noisiest and slowest thing you can do.
```
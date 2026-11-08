# SSH

_Port: 22_

## Service Usage

Connect with ssh
```bash
ssh -i keyfile $un@$ip
```
Create ssh key
```bash
ssh-keygen -t rsa -f mykey -N ''
```

## Config Paths

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

### Common gotchas
```text
- "Permission denied (publickey)" = trying to use key but no key/password auth. Check which auth methods offered.
- Key perms too open? ssh ignores them — chmod 600 first.
- Strict host key warnings on repeat? Use -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null.
- SSH brute = last resort; it's the noisiest and slowest thing you can do.
```
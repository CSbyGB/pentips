# MSSQL

> *Source CTF and HTB Academy*

- Usually on port 1433
- [Official documentation](https://www.microsoft.com/en-us/sql-server/sql-server-2019)
- [SQL Server Management Studio](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)
- [System Databases MS](https://learn.microsoft.com/en-us/sql/relational-databases/databases/system-databases?view=sql-server-ver15)

Microsoft SQL (MSSQL) is Microsoft's SQL-based relational database management system. Unlike MySQL, MSSQL is closed source and was initially written to run on Windows operating systems. It is popular among database administrators and developers when building applications that run on Microsoft's .NET framework due to its strong native support for .NET. There are versions of MSSQL that will run on Linux and MacOS, but we will more likely come across MSSQL instances on targets running Windows.  

## Clients to access an MSSQL db

- [mssql-cli](https://learn.microsoft.com/en-us/sql/tools/mssql-cli?view=sql-server-ver15)
- [SQL Server Poweshell](https://learn.microsoft.com/en-us/sql/powershell/sql-server-powershell?view=sql-server-ver15)
- [HeidiSQL](https://www.heidisql.com/)
- [Macsqlcient](https://www.macsqlclient.com/)
- [Impacket - mssqlcient](https://github.com/fortra/impacket/blob/master/examples/mssqlclient.py)

## MSSQL Databases

|Default System Database|Description|
|-----------------------|-----------|
|`master`|Tracks all system information for an SQL server instance|
|`model`|Template database that acts as a structure for every new database created. Any setting changed in the model database will be reflected in any new database created after changes to the model database|
|`msdb`|The SQL Server Agent uses this database to schedule jobs & alerts|
|`tempdb`|Stores temporary objects|
|`resource`|Read-only database containing system objects included with SQL server|

## Dangerous Settings

- MSSQL clients not using encryption to connect to the MSSQL server
- The use of self-signed certificates when encryption is being used. It is possible to spoof self-signed certificates
- The use of named pipes
- Weak & default sa credentials. Admins may forget to disable this account

## Enumeration

### Nmap

- `sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248`

### Metasploit

- Module: auxiliary/scanner/mssql/mssql_ping

### Impacket - mssqlclient

- `sudo apt install python3-impacket` install impacket to access mssqlclient
- `impacket-mssqlclient Administrator@10.129.201.248 -windows-auth` connect
- `SELECT name, database_id, create_date  FROM sys.databases;` list the databases
- `use <dbname>;`
- `SELECT * FROM INFORMATION_SCHEMA.TABLES;` list tables

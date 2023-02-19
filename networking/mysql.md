# MySQL

> *Source CTF and HTB Academy*

- Usually on port 3306

MySQL is an open-source SQL relational database management system developed and supported by Oracle. A database is simply a structured collection of data organized for easy use and retrieval. The database system can quickly process large amounts of data with high performance.

- `sudo apt install mysql-server -y` install mysql server
- [See here for help with install](https://dev.mysql.com/doc/mysql-apt-repo-quick-guide/en/#apt-repo-fresh-install)
- On Parrot I had to
  - sudo apt install mariadb-client
  - sudo apt install mariadb-server

## SQL language

- [SQL Introduction on w3Schools](https://www.w3schools.com/sql/sql_intro.asp)

## Default Configuration

- `cat /etc/mysql/mysql.conf.d/mysqld.cnf | grep -v "#" | sed -r '/^\s*$/d'`

## Dangerous Settings

|Settings|Description|
|--------|-----------|
|`user`|Sets which user the MySQL service will run as.|
|`password`|Sets the password for the MySQL user.|
|`admin_address`|The IP address on which to listen for TCP/IP connections on the administrative network interface.|
|`debug`|This variable indicates the current debugging settings|
|`sql_warnings`|This variable controls whether single-row INSERT statements produce an information string if warnings occur.|
|`secure_file_priv`|This variable is used to limit the effect of data import and export operations.|

## Enumeration

### Nmap

- `sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*`

### Interaction with the server

- `mysql -u root -h 10.129.14.132`
- `mysql -u root -pP4SSw0rd -h 10.129.14.128`
- `show databases;` list databases
- `select version();` show the version
- `use <database>;` select a database
- `show tables;` list the tables
- `show columns from <table>;`
- `select * from <table>;`
- `select * from <table> where <column> = "<string>";` Example:`select host, unique_users from host_summary;`

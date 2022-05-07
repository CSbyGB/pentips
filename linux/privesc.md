# Linux privilege escalation

## System Enumeration

- `hostname` will return the hostname of the target machine
- `uname -a` Will print system information
- `/proc/version` provides information about the target system processes
- `/etc/issue` contains some information about the operating system but can be changed
- `ps` see the running processes
  - `ps -A` View all running processes
  - `ps axjf` View process tree
  - `ps aux` The aux option will show processes for all users (a), display the user that launched the process (u), and show processes that are not attached to a terminal (x). 
- `env` will show environmental variables

## User enumeration

- `ìd` general overview of the user’s privilege level and group memberships
- `/etc/passwd` list of users on the system
  - `cat /etc/passwd | grep home | cut -d ":" -f 1` this should return only users (and no service accounts)
- `history` will show previous commands
- `sudo -l` example

  ```
  sudo -l
  Matching Defaults entries for cerealkiller on ip-172-31-63-238:
  	env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
  User cerealkiller may run the following commands on ip-172-31-63-238:
	  (phantom) NOPASSWD: ALL
	  (vimuser) NOPASSWD: /usr/bin/vim
	  (nmapuser) NOPASSWD: /usr/bin/nmap
  ```

- `sudo -u phantom cat /home/phantom/flag.txt` execute a command with another user

## Network Enumeration

- `ifconfig` information about the network interfaces of the system
- `ip add` (similar to ifconfig)
- `ip route` show network routes
- `netstat` gather information on existing connections
  - `netstat -a` shows all listening ports and established connections.
  - `netstat -at` or `netstat -au` can also be used to list TCP or UDP protocols respectively.
  - `netstat -l` list ports in “listening” mode. These ports are open and ready to accept incoming connections. 
    This can be used with the “t” option to list only ports that are listening using the TCP protocol (below)
  - `netstat -s` list network usage statistics by protocol (below) This can also be used with the `-t` or `-u` options to limit the output to a specific protocol.
  - `netstat -ltp` list connections with the service name and PID information and listening ports.
  - `netstat -i` Shows interface statistics.
  - `netstat -ano` `-a` Display all sockets, `-n` Do not resolve names, `-o` Display timers

## Misc CTF tricks

- Being creative with nmap if we do not have rights to read a specific file
  
  ```
  sudo -u nmapuser nmap -iL flag.txt 127.0.0.1
  Starting Nmap 7.60 ( https://nmap.org ) at 2020-06-21 02:54 UTC
  Failed to resolve "HF-B2C56B421F6229316B00A973586AAAD1".
  WARNING: No targets were specified, so 0 hosts scanned.
  Nmap done: 0 IP addresses (0 hosts up) scanned in 0.04 seconds
  ```
  
## Tools

- [LinEnum](https://github.com/rebootuser/LinEnum )
- [LinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite)

## Resources

{% embed url="https://academy.tcm-sec.com/p/linux-privilege-escalation" %} Linux Privesc on TCM-Security Academy {% endembed %}  
{% embed url="https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/" %} Basic Linux Privesc {% endembed %}  
{% embed url="https://gtfobins.github.io/" %} GTFoBins a curated list of Unix binaries {% endembed %}  
{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md" %} Linux - Privilege Escalation - Payload all the things {% endembed %}  
{% embed url="https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation-checklist" %} Checklist - Linux Privilege Escalation {% endembed %}  
{% embed url="https://sushant747.gitbooks.io/total-oscp-guide/content/privilege_escalation_-_linux.html" %} Sushant 747's Guide (Country dependant - may need VPN){% endembed %}  
{% embed url="hhttps://github.com/TCM-Course-Resources/Linux-Privilege-Escalation-Resources" %} Linux-Privilege-Escalation-Resources {% endembed %}  

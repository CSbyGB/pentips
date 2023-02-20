# R-Services

> *Source CTF and HTB Academy*

- Usually on port 512, 513, and 514

R-Services are a suite of services hosted to enable remote access or issue commands between Unix hosts over TCP/IP.  
only accessible through a suite of programs known as r-commands. They are most commonly used by commercial operating systems such as Solaris, HP-UX, and AIX.  

## R-Commands suite

- [Berkeley r-commands](https://en.wikipedia.org/wiki/Berkeley_r-commands)

- rcp (remote copy)
- rexec (remote execution)
- rlogin (remote login)
- rsh (remote shell)
- rstat
- ruptime
- rwho (remote who)

|Command|Service Daemon|Port|Transport Protocol|Description|
|-------|--------------|----|------------------|-----------|
|rcp|rshd|514|TCP|Copy a file or directory bidirectionally from the local system to the remote system (or vice versa) or from one remote system to another. It works like the cp command on Linux but provides no warning to the user for overwriting existing files on a system.|
|rsh|rshd|514|TCP|Opens a shell on a remote machine without a login procedure. Relies upon the trusted entries in the /etc/hosts.equiv and .rhosts files for validation.|
|rexec|rexecd|512|TCP|Enables a user to run shell commands on a remote machine. Requires authentication through the use of a username and password through an unencrypted network socket. Authentication is overridden by the trusted entries in the /etc/hosts.equiv and .rhosts files.
|rlogin|rlogind|513|TCP|Enables a user to log in to a remote host over the network. It works similarly to telnet but can only connect to Unix-like hosts. Authentication is overridden by the trusted entries in the /etc/hosts.equiv and .rhosts files.|

The /etc/hosts.equiv file contains a list of trusted hosts and is used to grant access to other systems on the network. When users on one of these hosts attempt to access the system, they are automatically granted access without further authentication.

The hosts.equiv and .rhosts files contain a list of hosts (IPs or Hostnames) and users that are trusted by the local host when a connection attempt is made using r-commands.  
The hosts.equiv file is recognized as the global configuration regarding all users on a system, whereas .rhosts provides a per-user configuration.  
Misconfigurations in either of these files can allow an attacker to authenticate as another user without credentials, with the potential for gaining code execution.  

- `cat /etc/hosts.equiv`

## Enumeration

### Nmap

- `sudo nmap -sV -p 512,513,514 10.0.17.2`

### Logging in

- `rlogin 10.0.17.2 -l account-name`
- `rwho` list authenticated users

### List authenticated users

- `rusers -al 10.0.17.5`

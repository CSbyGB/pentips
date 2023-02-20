# WinRM

> *Source CTF and HTB Academy*

- Usually on port 5985, 5986

The Windows Remote Management (WinRM) is a simple Windows integrated remote management protocol based on the command line. WinRM uses the Simple Object Access Protocol (SOAP) to establish connections to remote hosts and their applications. Therefore, WinRM must be explicitly enabled and configured starting with Windows 10.  

## Enumeration

### Nmap

- `nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n`

### Evil-winrm

- `evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!`

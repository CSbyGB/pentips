# WinRM

> *Source CTF and HTB Academy*

- Usually on port 5985, 5986

The Windows Remote Management (WinRM) is a simple Windows integrated remote management protocol based on the command line. WinRM uses the Simple Object Access Protocol (SOAP) to establish connections to remote hosts and their applications. Therefore, WinRM must be explicitly enabled and configured starting with Windows 10.  

## Enumeration

### Nmap

- `nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n`

### Evil-winrm

- `sudo gem install evil-winrm` install
- `evil-winrm -i <target-IP> -u <username> -p <password>` Usage
- `evil-winrm -i 10.129.201.248 -u user -p password` Example

### Crackmapexec

- `crackmapexec winrm 10.129.42.197 -u user.list -p password.list`

## Resources

- [CrackMapExec](https://mpgn.gitbook.io/crackmapexec/)
- [Winrm Pentest - Raj Chandel](https://www.hackingarticles.in/winrm-penetration-testing/)
- [Abusing Windows Remote Management (WinRM) with Metasploit - Rapid7](https://www.rapid7.com/blog/post/2012/11/08/abusing-windows-remote-management-winrm-with-metasploit/)
- [Winrm - Lisandre](https://lisandre.com/archives/12597)
- [5985,5986 - Pentesting WinRM - Hacktricks](https://book.hacktricks.xyz/network-services-pentesting/5985-5986-pentesting-winrm)
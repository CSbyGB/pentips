# RDP

> *Source CTF and HTB Academy*

- Usually on port 3389

The Remote Desktop Protocol (RDP) is a protocol developed by Microsoft for remote access to a computer running the Windows operating system. This protocol allows display and control commands to be transmitted via the GUI encrypted over IP networks.  

- [Official Documentation](https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol)

## Enumeration

### Nmap

- `nmap -sV -sC 10.129.201.248 -p3389 --script rdp*`
- `nmap -sV -sC 10.129.201.248 -p3389 --packet-trace --disable-arp-ping -n`

### RDP Security check

#### Installation

- `sudo cpan` type yes when prompted
  - `install Encoding::BER`
- `git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check`
- `./rdp-sec-check.pl 10.129.201.248`

### Password Attack

#### hydra

- `hydra -L user.list -P password.list rdp://10.129.42.197`

### Connect to RDP

- `xfreerdp /v:<target-IP> /u:<username> /p:<password>` usage. Example `xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248`
- You can also use [Remmina](https://remmina.org/)
- Or rdesktop `rdesktop 10.0.0.251`

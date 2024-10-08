# SMB

> *Source CTF and HTB Academy*

- Usually on ports 137, 138, 139, 445

[Server Message Block (SMB)](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb/f210069c-7086-4dc2-885e-861d837df688) is a client-server protocol that regulates access to files and entire directories and other network resources such as printers, routers, or interfaces released for the network.  
With the free software project Samba, there is also a solution that enables the use of SMB in Linux and Unix distributions and thus cross-platform communication via SMB.  
Access rights are defined by Access Control Lists (ACL). They can be controlled in a fine-grained manner based on attributes such as execute, read, and full access for individual users or user groups. The ACLs are defined based on the shares and therefore do not correspond to the rights assigned locally on the server.  

- [SMB examples](https://winprotocoldoc.blob.core.windows.net/productionwindowsarchives/MS-SMB2/%5bMS-SMB2%5d.pdf#%5B%7B%22num%22%3A920%2C%22gen%22%3A0%7D%2C%7B%22name%22%3A%22XYZ%22%7D%2C69%2C738%2C0%5D)

## Samba

Samba implements the [Common Internet File System (CIFS)](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cifs/934c2faa-54af-4526-ac74-6a24d126724e) network protocol. CIFS is a "dialect" of SMB.  
CIFS is the extension of the SMB protocol. So when we pass SMB commands over Samba to an older NetBIOS service, it usually connects to the Samba server over TCP ports 137, 138, 139, but CIFS uses TCP port 445 only.  
With version 3, the Samba server gained the ability to be a full member of an Active Directory domain. With version 4, Samba even provides an Active Directory domain controller. It contains several so-called daemons for this purpose - which are Unix background programs. The SMB server daemon (smbd) belonging to Samba provides the first two functionalities, while the NetBIOS message block daemon (nmbd) implements the last two functionalities. The SMB service controls these two background programs.

### Default configuration

- [Documentation](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html)
- `cat /etc/samba/smb.conf | grep -v "#\|\;"` see default configuration

|Setting|Description|
|-------|-----------|
|`[sharename]`|The name of the network share.|
|`workgroup = WORKGROUP/DOMAIN`|Workgroup that will appear when clients query.|
|`path = /path/here/`|The directory to which user is to be given access.|
|`server string = STRING`|The string that will show up when a connection is initiated.|
|`unix password sync = yes`|Synchronize the UNIX password with the SMB password?|
|`usershare allow guests = yes`|Allow non-authenticated users to access defined shared?|
|`map to guest = bad user`|What to do when a user login request doesn't match a valid UNIX user?|
|`browseable = yes`|Should this share be shown in the list of available shares?|
|`guest ok = yes`|Allow connecting to the service without using a password?|
|`read only = yes`|Allow users to read files only?|
|`create mask = 0700`|What permissions need to be set for newly created files?|

### Dangerous Settings

|Setting|Description|
|-------|-----------|
|`browseable = yes`|Allow listing available shares in the current share?|
|`read only = no`|Forbid the creation and modification of files?|
|`writable = yes`|Allow users to create and modify files?|
|`guest ok = yes`|Allow connecting to the service without using a password?|
|`enable privileges = yes`|Honor privileges assigned to specific SID?|
|`create mask = 0777`|What permissions must be assigned to the newly created files?|
|`directory mask = 0777`|What permissions must be assigned to the newly created directories?|
|`logon script = script.sh`|What script needs to be executed on the user's login?|
|`magic script = script.sh`|Which script should be executed when the script gets closed?|
|`magic output = script.out`|Where the output of the magic script needs to be stored?|

## Enumerate SMB

### Nmap

- `sudo nmap 10.129.14.128 -sV -sC -p139,445`
- You can use dedicated smb scripts

### rpcclient

- `rpcclient -U "" 10.129.14.128`

|Query|Description|
|-----|-----------|
|`srvinfo`|Server information.|
|`enumdomains`|Enumerate all domains that are deployed in the network.|
|`querydominfo`|Provides domain, server, and user information of deployed domains.|
|`netshareenumall`|Enumerates all available shares.|
|`netsharegetinfo <share>`|Provides information about a specific share.
|`enumdomusers`|Enumerates all domain users.|
|`queryuser <RID>`|Provides information about a specific user.|
|`querygroup <RID>`|Provides information about a specific group.|

- Sometimes not all commands are available.
- `for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done` bruteforce userid

### Impacket Samrdump

- `samrdump.py 10.129.14.128`

### Enum with Metasploit

- `msfconsole`
- `use auxiliary/scanner/smb/smb_version`
- `options`
- `set RHOSTS IP-ADD` (in my example instead of IP-ADD I will put 10.0.2.4)
- `run`
![image](https://user-images.githubusercontent.com/96747355/175833442-8a36eb1a-d065-4b7d-8b55-af90ba1d75fb.png)  
- We see the version of Samba so it is something that is going to be worth writing down in our notes.

### smbclient

- Tool that will help us to list shares or see useful info
- `smbclient -L \\10.10.55.112` list shares
- `smbclient -L IP-ADD` (in my example instead of IP-ADD I will put 10.0.2.4)  
![image](https://user-images.githubusercontent.com/96747355/175833616-0eb455e8-ed55-48e6-abfb-64908fac28a8.png)  
- Connect anonymously `smbclient --no-pass \\\\10.10.10.100\\SHARE` or `smbclient //10.10.10.134/SHARE`
- Use `-N` option in smbclient to suppress the password prompt
- `smbclient -U user \\\\10.129.42.197\\SHARENAME`

### SMBmap

- [Github repo](https://github.com/ShawnDEvans/smbmap)
- `smbmap -H 192.168.1.40`

### Enum4linux

#### Installation

```bash
sudo apt install samba-client
sudo apt-get install samba-common-bin
git clone https://github.com/cddmp/enum4linux-ng.git
cd enum4linux-ng
python3 -m venv .
pip3 install -r requirements.txt
source bin/activate
```

#### Commands

- `enum4linux -a 10.10.10.100` output all sorts of info

### Crackmapexec

- [Github repo](https://github.com/Porchetta-Industries/CrackMapExec)
- `crackmapexec smb 10.129.14.128 --shares -u '' -p ''`

### Password attack

#### Hydra

- `hydra -L user.list -P password.list smb://10.129.42.197`

#### Pass attack with Metasploit

```bash
use auxiliary/scanner/smb/smb_login
options
set user_file user.list
set pass_file password.list
set rhosts 10.129.42.197
run
```

## What to try

- `smbclient --no-pass \\\\10.10.10.100\\SHARE` connect to a share anonymously
- You might find interesting files this way
- Check out the win version to see if it is vulnerable to anything (eternal blue for example)
- We can also try this command `smbclient //10.10.10.134/SHARE`
- `!<cmd>` run local system command even if connected to smb (exanple: `!ls`)
- `get` Download a file from smb
- `smbstatus` check connections on smb server

## Resources

{% embed url="https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/" %} A little guide to SMB Enumeration {% endembed %}  
{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-smb" %} Pentesting SMB - Hacktricks {% endembed %}  
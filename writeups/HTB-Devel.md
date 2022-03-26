# Hackthebox - Devel

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T4 -sC -sV -p- --min-rate=1000 10.10.10.5
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-26 10:58 EDT
Nmap scan report for 10.10.10.5
Host is up (0.028s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  01:06AM       <DIR>          aspnet_client
| 03-25-22  08:20PM                 2924 devel.aspx
| 03-17-17  04:37PM                  689 iisstart.htm
|_03-17-17  04:37PM               184946 welcome.png
80/tcp open  http    Microsoft IIS httpd 7.5
|_http-title: IIS7
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 100.38 seconds
```

## FTP

We are allowed to connect anonymously, when looking at the files listed on the nmap scan, it seems that these are the files available on the webserver.  
It seems like we can list directory  
Can we upload files and access them through the web server?
```
┌──(root💀kali)-[~]
└─# ftp 10.10.10.5                                 
Connected to 10.10.10.5.
220 Microsoft FTP Service
Name (10.10.10.5:root): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> passive
Passive mode: on; fallback to active mode: on.
ftp> ls
229 Entering Extended Passive Mode (|||49210|)
150 Opening ASCII mode data connection.
03-18-17  01:06AM       <DIR>          aspnet_client
03-25-22  08:20PM                 2924 devel.aspx
03-17-17  04:37PM                  689 iisstart.htm
03-17-17  04:37PM               184946 welcome.png
```
We can create an hello.html file with just `hello` in it.
```
ftp> put hello.html
local: hello.html remote: hello.html
229 Entering Extended Passive Mode (|||49220|)
125 Data connection already open; Transfer starting.
100% |******************************************************************************************************************************************************************|     7      341.79 KiB/s    --:-- ETA
226 Transfer complete.
7 bytes sent in 00:00 (0.28 KiB/s)
```

We can see it 

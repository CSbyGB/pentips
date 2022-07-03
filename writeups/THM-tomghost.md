# TryHackme - Tomghost - Linux

- [Room](https://tryhackme.com/room/tomghost)

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T4 -sC -sV -O -Pn -p- 10.10.80.104                                                                                                                                                                                             1 ⨯
Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-03 10:16 EDT
Nmap scan report for 10.10.80.104
Host is up (0.22s latency).
Not shown: 65531 closed tcp ports (reset)
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f3:c8:9f:0b:6a:c5:fe:95:54:0b:e9:e3:ba:93:db:7c (RSA)
|   256 dd:1a:09:f5:99:63:a3:43:0d:2d:90:d8:e3:e1:1f:b9 (ECDSA)
|_  256 48:d1:30:1b:38:6c:c6:53:ea:30:81:80:5d:0c:f1:05 (ED25519)
53/tcp   open  tcpwrapped
8009/tcp open  ajp13      Apache Jserv (Protocol v1.3)
| ajp-methods: 
|_  Supported methods: GET HEAD POST OPTIONS
8080/tcp open  http       Apache Tomcat 9.0.30
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/9.0.30
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=7/3%OT=22%CT=1%CU=30334%PV=Y%DS=4%DC=I%G=Y%TM=62C1A6D5
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=105%TI=Z%CI=I%II=I%TS=8)OPS(
OS:O1=M506ST11NW7%O2=M506ST11NW7%O3=M506NNT11NW7%O4=M506ST11NW7%O5=M506ST11
OS:NW7%O6=M506ST11)WIN(W1=68DF%W2=68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(
OS:R=Y%DF=Y%T=40%W=6903%O=M506NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS
OS:%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=
OS:Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=
OS:R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T
OS:=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=
OS:S)

Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 548.88 seconds
```

## Port 8080

- We have a tomcat server  
![image](https://user-images.githubusercontent.com/96747355/177050281-8c89a45b-b168-4bbb-8e59-417cf7d0077f.png)
- We can not access Server Status, manager app and host manager (access denied)

### Gobuster

## Port 8009

- This port was unexpected. When looking up online there is a reference to CVE-2020-1938 aka Ghostcat so with the name of the room this is a big hint that we need to use it.
- As usual hacktricks have a great resource about it [here](https://book.hacktricks.xyz/network-services-pentesting/8009-pentesting-apache-jserv-protocol-ajp)
- Let's get the [exploit](https://www.exploit-db.com/exploits/48143) mentioned in the article and try it out
- It is specify that we need to launch it like this `usage: 48143 [-h] [-p PORT] [-f FILE] target`
- Let's first try to get `WEB-INF/web.xml` And it works we have creds!  
![image](https://user-images.githubusercontent.com/96747355/177053568-a50be767-3b09-4b54-b642-6a951c822c4d.png)  
- The creds works for ssh 
![image](https://user-images.githubusercontent.com/96747355/177053674-b5e9376b-7d5c-4a02-a324-41e97a30375c.png)  
- If we do an ls on the home folder, we have 2 directories merlin and the one of the user we got initially. The user.txt flag is in the merlin directory.  
![image](https://user-images.githubusercontent.com/96747355/177053844-db23a335-4dd7-4020-b601-60ba6060ce00.png)  
- 
**COMING SOON**

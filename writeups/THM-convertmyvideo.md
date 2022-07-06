# TryHackMe - ConvertMyVideo - Linux

- [Room](https://tryhackme.com/room/convertmyvideo)

## Nmap

```
Starting Nmap 7.60 ( https://nmap.org ) at 2022-07-05 21:48 BST
Stats: 0:20:37 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 99.99% done; ETC: 22:09 (0:00:00 remaining)
Nmap scan report for ip-10-10-68-80.eu-west-1.compute.internal (10.10.68.80)
Host is up (0.00047s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 65:1b:fc:74:10:39:df:dd:d0:2d:f0:53:1c:eb:6d:ec (RSA)
|   256 c4:28:04:a5:c3:b9:6a:95:5a:4d:7a:6e:46:e2:14:db (ECDSA)
|_  256 ba:07:bb:cd:42:4a:f2:93:d1:05:d0:b3:4c:b1:d9:b1 (EdDSA)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
MAC Address: 02:61:5C:CF:D0:3B (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.60%E=4%D=7/5%OT=22%CT=1%CU=34666%PV=Y%DS=1%DC=D%G=Y%M=02615C%TM
OS:=62C4A916%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10A%TI=Z%CI=Z%TS=A)
OS:SEQ(SP=105%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M2301ST11NW7%O2=M230
OS:1ST11NW7%O3=M2301NNT11NW7%O4=M2301ST11NW7%O5=M2301ST11NW7%O6=M2301ST11)W
OS:IN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F
OS:507%O=M2301NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)
OS:T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%
OS:S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(
OS:R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0
OS:%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1381.76 seconds
```

## Port 80

- We have a page with a file upload functionality to convert a video  
![image](https://user-images.githubusercontent.com/96747355/177418155-b8a3bc88-c5ac-4aaf-b492-462d01e2611a.png)  

### Gobuster

```
┌──(root💀kali)-[/home/kali/Documents]
└─# gobuster dir -u http://10.10.158.186/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt                                                                                                            1 ⨯
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.158.186/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/07/06 17:18:46 Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 315] [--> http://10.10.158.186/images/]
/admin                (Status: 401) [Size: 460]                                   
/js                   (Status: 301) [Size: 311] [--> http://10.10.158.186/js/]    
/tmp                  (Status: 301) [Size: 312] [--> http://10.10.158.186/tmp/]
```

## Questions

- What is the name of the secret folder? `admin` (we get this using gobuster, see above)
- What is the user to access the secret folder?
- I let you find the flags on your own :)

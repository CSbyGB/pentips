# TryHackMe - LazyAdmin - Linux

## Nmap

```bash
┌──(root💀kali)-[~]
└─# nmap -T4 -sC -sV -O -Pn -p- 10.10.1.0    
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-28 15:50 EDT
Nmap scan report for 10.10.1.0
Host is up (0.24s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 49:7c:f7:41:10:43:73:da:2c:e6:38:95:86:f8:e0:f0 (RSA)
|   256 2f:d7:c4:4c:e8:1b:5a:90:44:df:c0:63:8c:72:ae:55 (ECDSA)
|_  256 61:84:62:27:c6:c3:29:17:dd:27:45:9e:29:cb:90:5e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=6/28%OT=22%CT=1%CU=37647%PV=Y%DS=4%DC=I%G=Y%TM=62BB608
OS:E%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=107%TI=Z%CI=Z%TS=A)SEQ(SP=1
OS:04%GCD=1%ISR=107%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M506ST11NW7%O2=M506ST11NW7%O
OS:3=M506NNT11NW7%O4=M506ST11NW7%O5=M506ST11NW7%O6=M506ST11)WIN(W1=68DF%W2=
OS:68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=40%W=6903%O=M506NNSN
OS:W7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%D
OS:F=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O
OS:=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W
OS:=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%R
OS:IPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1305.33 seconds                                                         
```

## Port 80

![image](https://user-images.githubusercontent.com/96747355/176272925-590af02b-7503-4fae-895c-0e68fee1a4d6.png)

### Directory busting

- With gobuster we find a directory `content`    
```
┌──(root💀kali)-[~]
└─# gobuster dir -u http://10.10.1.0/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt                      
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.1.0/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/06/28 15:57:42 Starting gobuster in directory enumeration mode
===============================================================
/content              (Status: 301) [Size: 308] [--> http://10.10.1.0/content/]
Progress: 43234 / 87665 (49.32%)                                              [ERROR] 2022/06/28 16:16:20 [!] Get "http://10.10.1.0/STRANGE": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
                                                                               
===============================================================
2022/06/28 16:38:57 Finished
===============================================================
```
![image](https://user-images.githubusercontent.com/96747355/176273862-d0452342-7829-42de-9daf-709a8360adf8.png)  
- It is worth launching gobuster inside content as well. We did find other things there too  
```
┌──(root💀kali)-[~]
└─# gobuster dir -u http://10.10.1.0/content/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.1.0/content/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/06/28 17:05:29 Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 315] [--> http://10.10.1.0/content/images/]
/js                   (Status: 301) [Size: 311] [--> http://10.10.1.0/content/js/]    
/inc                  (Status: 301) [Size: 312] [--> http://10.10.1.0/content/inc/]   
/as                   (Status: 301) [Size: 311] [--> http://10.10.1.0/content/as/]    
/_themes              (Status: 301) [Size: 316] [--> http://10.10.1.0/content/_themes/]
/attachment           (Status: 301) [Size: 319] [--> http://10.10.1.0/content/attachment/]

```
- And we have a login portal http://10.10.1.0/content/as/  
![image](https://user-images.githubusercontent.com/96747355/176288549-21b1f58e-e436-44db-bea8-6ba7fd437029.png)  
- We could search for the admin user and try to use this exploit [here](https://vulners.com/securityvulns/SECURITYVULNS:DOC:25071)
- We find this interesting exploit by googlin about [sweetrice cms](https://www.exploit-db.com/exploits/40718)
- We indeed find a sql file here http://10.10.1.0/content/inc/mysql_backup/
- We can also find a list of files as directory listing is enabled http://10.10.1.0/content/inc/
- With google research we find in the exploits a reference to the plugin directory http://10.10.1.0/content/_plugin/

## Resources

https://www.cvedetails.com/vulnerability-list/vendor_id-8230/product_id-18429/Basic-cms-Sweetrice.html
https://packetstormsecurity.com/files/83274/SweetRice-0.5.3-Remote-Local-File-Inclusion.html
https://www.exploit-db.com/exploits/40718
https://www.exploit-db.com/exploits/40716
https://packetstormsecurity.com/files/139521/SweetRice-1.5.1-Code-Execution.html
https://www.cvedetails.com/cve/CVE-2009-4231/
https://web.archive.org/web/20200229165936/http://www.basic-cms.org/



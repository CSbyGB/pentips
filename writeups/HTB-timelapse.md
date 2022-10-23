# Hackthebox - Timelapse - Easy

![Timelapse](../.res/2022-10-23-13-29-22.png)

- [Box on Hackthebox](https://app.hackthebox.com/machines/452)

## Nmap

```bash
┌──(kali㉿kali)-[~/Documents/timelapse]
└─$ sudo nmap -T4 -sC -sV -O -Pn -p- 10.10.11.152                                                                                                                                                                                      130 ⨯
Starting Nmap 7.92 ( https://nmap.org ) at 2022-10-23 13:31 EDT
Nmap scan report for 10.10.11.152
Host is up (0.023s latency).
Not shown: 65518 filtered tcp ports (no-response)
PORT      STATE SERVICE           VERSION
53/tcp    open  domain            Simple DNS Plus
88/tcp    open  kerberos-sec      Microsoft Windows Kerberos (server time: 2022-10-24 01:33:42Z)
135/tcp   open  msrpc             Microsoft Windows RPC
139/tcp   open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp   open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?
3268/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
3269/tcp  open  globalcatLDAPssl?
5986/tcp  open  ssl/http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
| tls-alpn: 
|_  http/1.1
|_http-server-header: Microsoft-HTTPAPI/2.0
| ssl-cert: Subject: commonName=dc01.timelapse.htb
| Not valid before: 2021-10-25T14:05:29
|_Not valid after:  2022-10-25T14:25:29
|_ssl-date: 2022-10-24T01:35:18+00:00; +7h59m59s from scanner time.
9389/tcp  open  mc-nmf            .NET Message Framing
49667/tcp open  msrpc             Microsoft Windows RPC
49673/tcp open  msrpc             Microsoft Windows RPC
49674/tcp open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
49696/tcp open  msrpc             Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 7h59m58s, deviation: 0s, median: 7h59m58s
| smb2-time: 
|   date: 2022-10-24T01:34:40
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 201.02 seconds
```

## Enum checklist

- [x] 53/tcp    open  domain            Simple DNS Plus
- [ ] 88/tcp    open  kerberos-sec      Microsoft Windows Kerberos (server time: 2022-10-24 01:33:42Z)
- [ ] 135/tcp   open  msrpc             Microsoft Windows RPC
- [ ] 139/tcp   open  netbios-ssn       Microsoft Windows netbios-ssn
- [ ] 389/tcp   open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
- [ ] 445/tcp   open  microsoft-ds?
- [ ] 464/tcp   open  kpasswd5?
- [ ] 593/tcp   open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
- [ ] 636/tcp   open  ldapssl?
- [ ] 3268/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: timelapse.htb0., Site: Default-First-Site-Name)
- [ ] 3269/tcp  open  globalcatLDAPssl?
- [ ] 5986/tcp  open  ssl/http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
- [ ] 9389/tcp  open  mc-nmf            .NET Message Framing
- [ ] 49667/tcp open  msrpc             Microsoft Windows RPC
- [ ] 49673/tcp open  msrpc             Microsoft Windows RPC
- [ ] 49674/tcp open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
- [ ] 49696/tcp open  msrpc             Microsoft Windows RPC

## DNS

```bash
┌──(kali㉿kali)-[~/Documents/timelapse]
└─$ dig axfr @10.10.11.152 timelapse.htb                                                                                                                                                                                                 1 ⚙

; <<>> DiG 9.17.19-1-Debian <<>> axfr @10.10.11.152 timelapse.htb
; (1 server found)
;; global options: +cmd
; Transfer failed.
```

- Nothing here

## SMB

- We can list shares with smbclient

```bash
┌──(kali㉿kali)-[~/Documents/timelapse]
└─$ smbclient --no-pass -L 10.10.11.152                                                                                                                                                                                                  1 ⚙

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        Shares          Disk      
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.11.152 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```

## Kerberos

- User enumeration with kerbrute

```bash
┌──(kali㉿kali)-[~/Documents/timelapse]
└─$ ./kerbrute_linux_amd64 userenum -d timelapse.htb --dc 10.10.11.152 /usr/share/wordlists/SecLists/Usernames/xato-net-10-million-usernames.txt                                                                                         1 ⚙

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 10/23/22 - Ronnie Flathers @ropnop

2022/10/23 13:54:03 >  Using KDC(s):
2022/10/23 13:54:03 >   10.10.11.152:88

2022/10/23 13:54:14 >  [+] VALID USERNAME:       guest@timelapse.htb
2022/10/23 13:54:31 >  [+] VALID USERNAME:       administrator@timelapse.htb
2022/10/23 13:57:40 >  [+] VALID USERNAME:       Guest@timelapse.htb
2022/10/23 13:57:42 >  [+] VALID USERNAME:       Administrator@timelapse.htb
```

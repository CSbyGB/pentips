# Scanning and Enumeration

- The machine use in the lab here for the example is Kioptrix from vulnhub
- During this stage it is really important to take good notes. We have to write versions we find any information disclosed during this phase.

## Scanning with nmap

### Identify all the machines in a network

- `netdiscover -r 10.0.2.0/24`  
  
![image](https://user-images.githubusercontent.com/96747355/175752002-a1ad3fc9-ed96-4076-837d-d14d2c293113.png)  

### Scan with Nmap

- We can use the `-sS` flag it is suppose to be stealthy (it is much more picked up today than it used to be). It is called stealthy because it is going to send SYN and when it will receive the SYNACK it will send a RST instead of an ACK (it means that when the remote machine is going to say "I am open you can connect" it is going to send something like "I do not want to connect anymore")
- `nmap -T4 -p- -A 10.0.2.4`
- `nmap -sU -T4 -p 10.0.2.4` In case we want to scan udp it is better to make a scan similar to this one because udp takes a long time to scan

### Result

- Here is the result from our example
```
┌──(root💀kali)-[~]
└─# nmap -T4 -p- -A 10.0.2.4                                                                                                                                                                                                           130 ⨯
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-24 21:09 EDT
Nmap scan report for 10.0.2.4
Host is up (0.00081s latency).
Not shown: 65529 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 2.9p2 (protocol 1.99)
|_sshv1: Server supports SSHv1
| ssh-hostkey: 
|   1024 b8:74:6c:db:fd:8b:e6:66:e9:2a:2b:df:5e:6f:64:86 (RSA1)
|   1024 8f:8e:5b:81:ed:21:ab:c1:80:e1:57:a3:3c:85:c4:71 (DSA)
|_  1024 ed:4e:a9:4a:06:14:ff:15:14:ce:da:3a:80:db:e2:81 (RSA)
80/tcp    open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
| http-methods: 
|_  Potentially risky methods: TRACE
111/tcp   open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1          32768/tcp   status
|_  100024  1          32768/udp   status
139/tcp   open  netbios-ssn Samba smbd (workgroup: SKMYGROUP)
443/tcp   open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2009-09-26T09:32:06
|_Not valid after:  2010-09-26T09:32:06
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_ssl-date: 2022-06-25T05:10:01+00:00; +3h59m59s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_RC4_64_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|_    SSL2_RC4_128_WITH_MD5
|_http-title: 400 Bad Request
32768/tcp open  status      1 (RPC #100024)
MAC Address: 08:00:27:4B:3D:CA (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 2.4.X
OS CPE: cpe:/o:linux:linux_kernel:2.4
OS details: Linux 2.4.9 - 2.4.18 (likely embedded)
Network Distance: 1 hop

Host script results:
|_clock-skew: 3h59m58s
|_smb2-time: Protocol negotiation failed (SMB2)
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)

TRACEROUTE
HOP RTT     ADDRESS
1   0.81 ms 10.0.2.4

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.74 seconds
```

## Enumerating HTTP and HTTPS

### Default web page

- We can go check the page in the browser and see what we find if we have 30 or 443 open (or both)
- Default web page = automatic finding. Disclose info about the tecnology used: web server, version, hostname etc. = Information Disclosure

### Nikto

- We can also launch nikto `nikto -h http://host.com`
  - Example of nikto result  
![image](https://user-images.githubusercontent.com/96747355/175832200-f1f6511c-b3c7-4543-8f5b-ae7720829cdf.png)  
  - It finds possible vulnerability
  - Will even do some directory busting

### Dirbuster

- We can use dirbuster or gobuster to check if we find hidden directories (directory busting). Here is an example with dirbuster  
![image](https://user-images.githubusercontent.com/96747355/175832562-7dcac6df-58c6-4385-92b9-f2d4e11384e1.png)  
- Example of results in tree view  
![image](https://user-images.githubusercontent.com/96747355/175832618-f4e4f9e6-7f72-44c7-bb08-3e243faa5073.png)
- Example of results in list  
![image](https://user-images.githubusercontent.com/96747355/175832640-313cbe72-ce8a-49c0-9e4e-1327aa17f280.png)

### Source code

- We can select view source in the browser.
- In there we can check for comments, usernames, passwords, keys etc.

### Burpsuite

- We can use the repeater to inspect a request modify it and analyze the response

## Enumerate SMB

- Fileshare 

### Metasploit

- `msfconsole`
- `use auxiliary/scanner/smb/smb_version`
- `options`
- `set RHOSTS IP-ADD` (in my example instead of IP-ADD I will put 10.0.2.4)
- `run`
![image](https://user-images.githubusercontent.com/96747355/175833442-8a36eb1a-d065-4b7d-8b55-af90ba1d75fb.png)  
- We see the version of Samba so it is something that is going to be worth writing down in our notes.

### smbclient

- Tool that will help us to list shares or see useful info
- `smbclient -L IP-ADD` (in my example instead of IP-ADD I will put 10.0.2.4)  
![image](https://user-images.githubusercontent.com/96747355/175833616-0eb455e8-ed55-48e6-abfb-64908fac28a8.png)  

## Enumerate SSH

- We need to keep notes of the ssh version
- We can try to ssh to our target `ssh IP-ADD`. In our example we get this  
![image](https://user-images.githubusercontent.com/96747355/175833787-a2b8bcbd-05a9-4ecb-ac37-b584c896253c.png)  
- For this error we can try this `ssh 10.0.2.4 -oKexAlgorithms=+diffie-hellman-group1-sha1 `  
![image](https://user-images.githubusercontent.com/96747355/175834000-bc2e9cbe-5949-4836-88d7-c9d399f95054.png)  
- For this error we can try this `ssh 10.0.2.4 -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa `  
![image](https://user-images.githubusercontent.com/96747355/175834033-1c9a18f8-bb6f-4961-bbc1-3550da4dba45.png)  
- Finally for this error we can use this and will try to connect `ssh 10.0.2.4 -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa -c aes128-cbc`

## Research 

- After this enumeration we can look up things we wrote down in our notes.

### Search Engines

- Usually we can try to google `technology version exploit` for example `mod_ssl 2.8.4 exploit` If anything shows up we can add it in our notes along with link to exploits to try them out later

### searchsploit

- On kali we can use searchsploit `searchsploit technology partial-version` for example `searchsploit Samba 2` or `searchsploit Samba`

## Vulnerability Sanning with Nessus

- Get it [here](https://www.tenable.com/downloads/nessus?loginAttempted=true)
- `dpkg -i Nessus-10.2.0-ubuntu1110_amd64.deb` install it
- `/bin/systemctl start nessusd.service` start the service
- Then we go to https://127.0.0.1:8834/ to finish the install and configure it
- If you are in the login page you can find out which username is installed with `/opt/nessus/sbin/nessuscli lsuser`
- You can then change your password with `/opt/nessus/sbin/nessuscli chpasswd username`
- If you are in the setup it is going to ask which version, you need to choose essentials (this version is free and will allow you to scan only private IP address) and then you will be able to register a user.
- Here is and example of scan result (we ordered the vuln by severity by choosing disable groups in the settings wheel) 
![image](https://user-images.githubusercontent.com/96747355/175835352-16616ab5-4784-48da-80f8-37641bcca200.png) 
- We can then check the vulns out and see if we can exploit them.


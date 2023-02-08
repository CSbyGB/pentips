# Netcat

## What is it

Netcat, ncat, or nc, is an excellent network utility for interacting with TCP/UDP ports. It can be used for many things during a pentest. Its primary usage is for connecting to shells.  
In addition to that, netcat can be used to connect to any listening port and interact with the service running on that port.  
For example, SSH is programmed to handle connections over port 22 to send all data and keys.  
We can connect to TCP port 22 with netcat `netcat 10.10.10.10 22`

*Source HTB Academy*

## Transfert files with netcat

- See here my documentation on [how to transfert files with netcat](https://csbygb.gitbook.io/pentips/post-exploitation/files-transfert#netcat)

## Netcat alternatives 

### For Windows

- [Powercat](https://github.com/besimorhino/powercat)

### Socat

- [Socat](https://linux.die.net/man/1/socat)
- Can be used to upgrade shell to [fully interactive TTY shell](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/#method-2-using-socat)
- [Stand alone binary](https://github.com/andrew-d/static-binaries)
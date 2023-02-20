# Rsync

> *Source CTF and HTB Academy*

- Usually on port 873

Tool for locally and remotely copying files.

- [Rsync man page](https://linux.die.net/man/1/rsync)
- [873 - Pentesting Rsync on Hacktricks](https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync)

## Enumeration

### Nmap

- `sudo nmap -sV -p 873 127.0.0.1`

### Netcat

- `nc -nv 127.0.0.1 873` Probing for Accessible Shares

### Enumerate an open share

- `rsync -av --list-only rsync://127.0.0.1/dev`

## Resources

- [How to Rsync over SSH](https://phoenixnap.com/kb/how-to-rsync-over-ssh)

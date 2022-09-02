# Gaining an Initial foothold

## Nmap

Launch an nmap scan, my favorite is:  
- `sudo nmap -T4 -sC -sV -p- --min-rate=1000 IP-ADR`  
  The `--min-rate` is so that the packets are sent faster than what is specified, `-sC` is for the scripts, it will use default scripts for enum and `-sV` is for services, `-p-` specifies we want all ports not only the top ones.

<!-- TODO list all services with possible vulnerabilities for initial foothold -->

## FTP

- Check if anonymous FTP is enabled, if so check if you can downloads files. If so you can RCE.  
Check out [Hackthebox Devel's writeup](../writeups/HTB-Devel.md) to have an example of this

- If you do not have write access you could still find interesting and useful files like passwords or else.

## SMB

### Enumeration

- `smbclient -L \\10.10.55.112` list shares
- `enum4linux -a 10.10.10.100` output all sorts of info
- `smbmap -H 192.168.1.40`

### What to try

- `smbclient --no-pass \\\\10.10.10.100\\SHARE` connect to a share anonymously
- You might find interesting files this way
- Check out the win version to see if it is vulnerable to anything (eternal blue for example)

## Resources

{% embed url="https://www.hackingarticles.in/a-little-guide-to-smb-enumeration/" %} A little guide to SMB Enumeration {% endembed %}

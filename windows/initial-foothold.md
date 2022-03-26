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

# Nmap

## Remove noise when using nmap through proxychains

- Add `quiet_mode` to your proxychains conf file (you should just need to uncomment it usually around line 50 of the file)

## Scan all ports for services and with default script

- `sudo nmap -T4 -sC -sV -p- --min-rate=1000 IP-ADR`

## Host discovery on a network

- `sudo nmap -sn 10.10.110.0-254`

## Resources

{% embed url="https://nmap.org/book/man.html" %} Nmap Reference Guide {% endembed %}  

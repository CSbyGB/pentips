# Nmap

## Remove noise when using nmap through proxychains

- Add `quiet_mode` to your proxychains conf file (you should just need to uncomment it usually around line 50 of the file)

## Scan all ports for services and with default script

- `sudo nmap -T4 -sC -O -sV -p- --min-rate=1000 IP-ADR`
- `sudo nmap -T4 -sC -O -sV -p- IP-ADR`

## Aggressive scan of all port (will output OS as if you used `-O`, service versions as with `-sV` and script scanning `-sC` )

- `nmap -T4 -A -p- 10.10.10.97` This mode sends a lot more probes, and it is more likely to be detected, but provides a lot of valuable host information

## Host discovery on a network

- `sudo nmap -sn 10.10.110.0-254`

## Resources

{% embed url="https://nmap.org/book/man.html" %} Nmap Reference Guide {% endembed %}  

# Nmap

## Remove noise when using nmap through proxychains

- Add `quiet_mode` to your proxychains conf file (you should just need to uncomment it usually around line 50 of the file)

## Scan all ports for services and with default script

- `sudo nmap -T4 -sC -O -sV -p- --min-rate=1000 IP-ADR`
- `sudo nmap -T4 -sC -O -sV -p- IP-ADR`
- `sudo nmap -Pn -sV -sC -p- IP-ADR`

## Aggressive scan of all port (will output OS as if you used `-O`, service versions as with `-sV` and script scanning `-sC` )

- `nmap -T4 -A -p- 10.10.10.97` This mode sends a lot more probes, and it is more likely to be detected, but provides a lot of valuable host information

## Host discovery on a network

- `sudo nmap -sn 10.10.110.0-254`

## Debug

- If you get this error `Warning: 10.10.64.180 giving up on port because retransmission cap hit (2)`
- Try using `-T4` instead of `-T5`
- Check this post [here](https://stackoverflow.com/questions/14736530/nmap-warning-giving-up-on-port-because-retransmission-cap-hit-2) for more info on this error

## Resources

{% embed url="https://nmap.org/book/man.html" %} Nmap Reference Guide {% endembed %}  

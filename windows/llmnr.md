# LLMNR Poisoning

## Responder

- [Impacket](https://github.com/SecureAuthCorp/impacket): tool to abuse LLMNR:  
Best to run first thing in the morning  
```responder -I eth0 -rdwv```
And when there will be activity in the network we will be able to get hashes

## Hascat

- We use hashcat to crack the hashes we got previously
- When looking for a module in hascat we can grep on the hash we need:
```hashcat --help | grep NTLM```
- Command
```hashcat -m 5600 ntlmhash.txt /usr/share/wordlists/rockyou.txt```

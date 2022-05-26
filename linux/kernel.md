# Privesc via Kernel Exploit

## What is a kernel

![image](https://user-images.githubusercontent.com/96747355/167271686-de6d61de-0566-475e-9248-bac94de54d0e.png)  
- [See the definition on Wikiedia](https://en.wikipedia.org/wiki/Kernel_(operating_system))
- [A list of Kernel Exploits](https://github.com/lucyoa/kernel-exploits)

## How to privesc via kernel exploit

### Enumerate

- `uname -a` or `cat /etc/lsb-release `
- We can then google the version we get and see if anything comes out
- We can also use Linux exploit suggester and investigate the results


### Dirty Cow

- We can use this [exploit](https://www.exploit-db.com/exploits/40839)
- `gcc -pthread c0w.c -o cow`
- We are a low privilege user for now  
![image](https://user-images.githubusercontent.com/96747355/167271901-78383711-70af-455a-8120-f221f8375bcc.png)  
- `./cow`  
![image](https://user-images.githubusercontent.com/96747355/167271978-bed109e8-f527-4f8e-8303-a935a27150c6.png)
- `passwd` should let us be root

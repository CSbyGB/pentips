# Escalation via SUID

## Enumeration

- `find / -perm -u=s -type f 2>/dev/null` will list all the files with the suid perm
- You can see the suid perm in the permissions as an `s` like here:  
![image](https://user-images.githubusercontent.com/96747355/167308910-5b0a0157-6586-46bd-a213-42c229e996e8.png)  

## Exploitation

- Here again we can use [GTFOBins](https://gtfobins.github.io/#+suid) and check if our is in there and has documented exploitation


## Practice

- You see and example of this exploit with in [this writeup](../writeups/THM-vulnversity.md)

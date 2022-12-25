# POP

- Usually ports 110, 995

## Enumeration

- `openssl s_client -connect TARGET-IP:995 -crlf -quiet` (995 is the port)
- `sudo nmap -p 110,995 --script=pop3-* TARGET-IP` will try different enumeration including username enumeration

## Password spray & bruteforce

- `hydra pop3://TARGET-IP/TLS -L users.txt -P passwords.txt` 

## Connect to an email account with openssl

```
openssl s_client -connect TARGET-IP:995
USER username@domain.com
PASS password
```

## Resources

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-pop" %} Pentesting POP - Hacktricks {% endembed %}  
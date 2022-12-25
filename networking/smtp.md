# SMTP

- Usually on port 25, 465, 587

## Enumeration

- With telnet

```bash
telnet TARGET-IP 25
AUTH
```

## Connect to the email account

- `openssl s_client -starttls smtp -connect mail.target.com:587`

## Password spray and bruteforce

- `hydra -L users.txt TARGET-IP smtp-enum`

## Resources

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-smtp" %} Pentesting SMTP/s - Hacktricks {% endembed %}  
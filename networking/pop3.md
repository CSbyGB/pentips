# POP

> *Source CTF and HTB Academy*

- Usually ports 110, 995

POP3 provides listing, retrieving, and deleting emails as functions at the email server.  

## Commands

|Command|Description|
|-------|-----------|
|`USER username`|Identifies the user.|
|`PASS password`|Authentication of the user using its password.|
|`STAT`|Requests the number of saved emails from the server.|
|`LIST`|Requests from the server the number and size of all emails.|
|`RETR id`|Requests the server to deliver the requested email by ID.|
|`DELE id`|Requests the server to delete the requested email by ID.|
|`CAPA`|Requests the server to display the server capabilities.|
|`RSET`|Requests the server to reset the transmitted information.|
|`QUIT`|Closes the connection with the POP3 server.|

## Dangerous settings

Same as imap see [here](imap.md)

## Enumeration

- `openssl s_client -connect TARGET-IP:995 -crlf -quiet` (995 is the port)
- `sudo nmap 10.129.14.128 -sV -p110,995 -sC`
- `sudo nmap -p 110,995 --script=pop3-* TARGET-IP` will try different enumeration including username enumeration
- `openssl s_client -connect 10.129.14.128:pop3s`
- `curl -k 'pop3s://10.129.250.84' --user robin:robin -v`

## Password spray & bruteforce

- `hydra pop3://TARGET-IP/TLS -L users.txt -P passwords.txt`

## Connect to an email account with openssl

```bash
openssl s_client -connect TARGET-IP:995
USER username@domain.com
PASS password
```

## Resources

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-pop" %} Pentesting POP - Hacktricks {% endembed %}  

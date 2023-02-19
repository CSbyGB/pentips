# SMTP

> *Source CTF and HTB Academy*

- Usually on port 25, 465, 587

The Simple Mail Transfer Protocol (SMTP) is a protocol for sending emails in an IP network. It can be used between an email client and an outgoing mail server or between two SMTP servers. SMTP is often combined with the IMAP or POP3 protocols, which can fetch emails and send emails. In principle, it is a client-server-based protocol, although SMTP can be used between a client and a server and between two SMTP servers. In this case, a server effectively acts as a client.  
An essential function of an SMTP server is preventing spam using authentication mechanisms that allow only authorized users to send e-mails. For this purpose, most modern SMTP servers support the protocol extension ESMTP with SMTP-Auth. After sending his e-mail, the SMTP client, also known as Mail User Agent (MUA), converts it into a header and a body and uploads both to the SMTP server. This has a so-called Mail Transfer Agent (MTA), the software basis for sending and receiving e-mails. The MTA checks the e-mail for size and spam and then stores it. To relieve the MTA, it is occasionally preceded by a Mail Submission Agent (MSA), which checks the validity, i.e., the origin of the e-mail. This MSA is also called Relay server. These are very important later on, as the so-called Open Relay Attack can be carried out on many SMTP servers due to incorrect configuration.  

![packet](../.res/2023-02-19-09-59-45.png)

- ESMTP extended smtp: will return delivery confirmation and uses TLS
- When people talk about SMTP in general, they usually mean ESMTP

## Default configuration

- `cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"`

|Command|Description|
|-------|-----------|
|`AUTH PLAIN`|AUTH is a service extension used to authenticate the client.|
|`HELO`|The client logs in with its computer name and thus starts the session.|
|`MAIL FROM`|The client names the email sender.|
|`RCPT TO`|The client names the email recipient.|
|`DATA`|The client initiates the transmission of the email.|
|`RSET`|The client aborts the initiated transmission but keeps the connection between client and server.|
|`VRFY`|The client checks if a mailbox is available for message transfer.|
|`EXPN`|The client also checks if a mailbox is available for messaging with this command.|
|`NOOP`|The client requests a response from the server to prevent  disconnection due to time-out.|
|`QUIT`|The client terminates the session.|

## Dangerous Settings

- Open relay configuration `mynetworks = 0.0.0.0/0` With this setting, this SMTP server can send fake emails and thus initialize communication between multiple parties. Another attack possibility would be to spoof the email and read it.

## Enumeration

### Nmap

- `sudo nmap 10.129.14.128 -sC -sV -p25`
- `sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v`

### Telnet

```bash
telnet TARGET-IP 25
AUTH
```

- Example

```bash
telnet 10.129.14.128 25
HELO mail1.inlanefreight.htb
EHLO mail1
```

- `VRFY` can be used to enum users
- [SMTP errors and reply codes](https://serversmtp.com/smtp-error/)

## Connect to the email account

- `openssl s_client -starttls smtp -connect mail.target.com:587`

## Password spray and bruteforce

- `hydra -L users.txt TARGET-IP smtp-enum`

## Resources

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-smtp" %} Pentesting SMTP/s - Hacktricks {% endembed %}  
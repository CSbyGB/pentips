# Summary

## CS By GB - PenTips

* [Welcome to CSbyGB's Pentips](README.md)

## Networking, Protocols and Network pentest

* [Basics](networking/basics.md)
* [DNS](networking/dns.md)
* [FTP](networking/ftp.md)
* [HTTP & HTTPS](networking/http-https.md)
* [IMAP](networking/imap.md)
* [IPMI](networking/ipmi.md)
* [MSSQL](networking/mssql.md)
* [MYSQL](networking/mysql.md)
* [NFS](networking/nfs.md)
* [Oracle TNS](networking/oracle-tns.md)
* [POP3](networking/pop3.md)
* [RDP](networking/rdp.md)
* [RPC](networking/rpc.md)
* [Rservices](networking/rservices.md)
* [Rsync](networking/rsync.md)
* [SMB](networking/smb.md)
* [SMTP](networking/smtp.md)
* [SNMP](networking/snmp.md)
* [SSH](networking/ssh.md)
* [Winrm](networking/winrm.md)
* [WMI](networking/wmi.md)

## Ethical Hacking - General Methodology

* [Introduction](ethical-hacking-general-methodology/intro.md)
* [Information Gathering](ethical-hacking-general-methodology/information-gathering.md)
* [Scanning & Enumeration](ethical-hacking-general-methodology/scanenum.md)
* [Exploitation (basics)](ethical-hacking-general-methodology/exploitation.md)
* [Password Attacks](ethical-hacking-general-methodology/password-attacks.md)
* [Post Exploitation](ethical-hacking-general-methodology/post-exploitation.md)
* [Lateral Movement](ethical-hacking-general-methodology/lateral-movement.md)
* [Proof-of-Concept](ethical-hacking-general-methodology/proof-of-concept.md)
* [Post-Engagement](ethical-hacking-general-methodology/post-engagement.md)

## External Pentest

* [External Pentest](External-Pentest/external-pentest.md)

## Web Pentesting

* [Introduction to HTTP and web](web-pentest/web.md)
* [Enumeration](web-pentest/enumeration.md)
* [OWASP Top 10](web-pentest/owaspTop10.md)
* [General Methodo & Misc Tips](web-pentest/Misc-Tips.md)
* [Web Services and API](web-pentest/API.md)
* [Vunerabilities and attacks](web-pentest/webvulns.md)
  * [Clickjacking](web-pentest/clickjacking.md)
  * [CORS (Misconfigurations)](web-pentest/cors.md)
  * [CSRF](web-pentest/csrf.md)
  * [SSRF](web-pentest/ssrf.md)
  * [Bypass captcha](web-pentest/captcha-bypass.md)
  * [Template Injection (client and server side)](web-pentest/template-injection.md)
  * [MFA bypass](web-pentest/mfa-bypass.md)
* [Exposed git folder](web-pentest/exposed-git.md)
* [Docker exploitation and Docker vulnerabilities](web-pentest/docker-exploitation.md)
* [Websockets](web-pentest/websockets.md)

## Mobile App Pentest

* [Android](mobile-app/android.md)
* [IOS](mobile-app/ios.md)

## Wireless Pentest

* [Wireless pentest](wireless-pentest/wireless.md)

## Cloud Pentest

* [Cloud Pentest](cloud/cloud.md)
* [Google Cloud Platform](cloud/gcp.md)
* [AWS](cloud/aws.md)

## Thick Client Pentest

* [Thick Client](thick-client/thick-client.md)

## Hardware Pentest

* [ATM](hardware-pentest/atm.md)
* [IoT](hardware-pentest/IoT.md)

## Secure Code Review

* [Secure code review](code-review/code-review.md)

## Checklist

* [Web Application and API Pentest Checklist](checklists/web-and-api-pentest-checklist.md)
* [Linux Privesc Checklist](checklists/linux-privesc-checklist.md)
* [Mobile App Pentest Checklist](checklists/mobile-app-pentest-checklist.md)

## Tools

* [Burpsuite](Tools/burpsuite.md)
* [CrackMapExec](Tools/crackmapexec.md)
* [Netcat and alternatives](Tools/netcat.md)
* [Nmap](Tools/nmap.md)
* [Nuclei](Tools/nuclei.md)
* [Evil Winrm](Tools/evil-winrm.md)
* [Metasploit](Tools/Metasploit.md)
* [Covenant](Tools/covenant.md)
* [Mimikatz](windows/mimikatz.md)
* [Passwords, Hashes and wordlist tools](Tools/passwords-tools.md)
* [WFuzz](Tools/wfuzz.md)
* [WPScan](Tools/wpscan.md)
* [Powershell Empire](Tools/empire-powershell.md)
* [Curl](Tools/curl.md)
* [Vulnerability Scanning tools](Tools/vulnerability-scanning-tools.md)
* [Payload Tools](Tools/payload-generation-tools.md)
* [Out of band Servers](Tools/out-of-band-servers.md)
* [STEWS](Tools/stews.md)
* [Websocat](Tools/websocat.md)

## VM and Labs

* [General tips](lab/labstips.md)
* [Setup your pentest lab](ethical-hacking-general-methodology/lab-setup.md)

## Linux

* [Initial Foothold](linux/initial-foothold.md)
* [Useful commands and tools for pentest on Linux](linux/useful.md)
* [Privilege Escalation](linux/privesc.md)
  * [Kernel Exploits](linux/kernel.md)
  * [Password and file permission](linux/pass-file-perm.md)
  * [Sudo](linux/sudo.md)
  * [SUID](linux/suid.md)
  * [Capabilities](linux/capabilities.md)
  * [Scheduled tasks](linux/cron.md)
  * [NFS Root Squashing](linux/nfs-root-squashing.md)
  * [Services](linux/services.md)
  * [PATH Abuse](linux/path.md)
  * [Wildcard Abuse](linux/wildcard.md)
  * [Privileged groups](linux/groups.md)
  * [Exploit codes Cheat Sheet](linux/cheat-sheet-exploits.md)

## Windows

* [Offensive windows](windows/offensive-win.md)
* [Enumeration and general Win tips](windows/initial-foothold.md)
* [Privilege Escalation](windows/privesc.md)
* [Active Directory](windows/Active-Directory.md)
* [Attacking Active Directory](windows/Attacking-AD.md)
  * [LLMNR Poisoning](windows/llmnr.md)
  * [SMB Relay Attacks](windows/smb-relay-attack.md)
  * [Shell Access](windows/shell-access.md)
  * [IPv6 Attacks](windows/ipv6-attacks.md)
  * [Passback Attacks](windows/passback.md)
  * [Abusing ZeroLogon](windows/zerologon.md)
* [Post-Compromise Enumeration](windows/post-compromise-enum.md)
  * [Powerview or SharpView (.NET equivalent)](/windows/powerview-sharpview.md)
  * [AD Manual Enumeration](windows/manual-enum-ad.md)
  * [Bloodhound](windows/bloodhound.md)
  * [Post Compromise Enumeration - Resources](windows/post-compromise-enumeration-resources.md)
* [Post Compromise Attacks](windows/post-compromise-attack.md)
  * [Pass the Password / Hash](windows/pass-hash.md)
  * [Token Impersonation - Potato attacks](windows/token-impersonation.md)
  * [Kerberos](windows/kerberoasting.md)
  * [GPP/cPassword Attacks](windows/gpp.md)
  * [URL File Attack](windows/url-file-attack.md)
  * [PrintNightmare](windows/printNightmare.md)
  * [Printer Bug](windows/printer-bug.md)
  * [AutoLogon exploitation](windows/autoLogon.md)
  * [Always Installed Elevated exploitation](windows/alwaysinstallelevated.md)
  * [UAC Bypass](windows/UACbypass.md)
  * [Abusing ACL](windows/abusing-acl.md)
  * [Unconstrained Delegation](windows/unconstrained-delegation.md)
* [Persistence](windows/persistence.md)
* [AV Evasion](windows/AVevasion.md)
* [Weaponization](windows/weaponization.md)
* [Useful commands in Powershell, CMD and Sysinternals](windows/powershell-cmd.md)
* [Windows Internals](windows/internals.md)

## Programming

* [Python programming](programming/python.md)
* [My scripts](programming/myscripts.md)
* [Kotlin](programming/kotlin.md)

## Binary Exploitation

* [Assembly](Binary-Exploitation/Assembly.md)
* [Buffer Overflow - Stack based - Winx86](Binary-Exploitation/buffer-overflow-stack-win86.md)
* [Buffer Overflow - Stack based - Linux x86](Binary-Exploitation/buffer-overflow-stack-linux.md)

## OSINT

* [OSINT](OSINT/osint.md)
* [Create an OSINT lab](OSINT/lab.md)
* [Sock Puppets](OSINT/sock-puppets.md)
* [Search engines](OSINT/search-engines.md)
* [OSINT Images](OSINT/image-osint.md)
* [OSINT Email](OSINT/email.md)
* [OSINT Password](OSINT/password.md)
* [OSINT Usernames](OSINT/usernames.md)
* [OSINT People](OSINT/people.md)
* [OSINT Social Media](OSINT/social-media.md)
* [OSINT Websites](OSINT/website.md)
* [OSINT Business](OSINT/business.md)
* [OSINT Wireless](OSINT/wireless.md)
* [OSINT Tools](OSINT/tools.md)
* [Write an OSINT report](OSINT/report.md)

## Pentester hardware toolbox

* [Flipper Zero](pentester-toolbox/flipper.md)
* [OMG cables](pentester-toolbox/omgcables.md)
* [Rubber ducky](pentest-toolbox/rubberducky.md)

## Post Exploitation

* [File transfers between target and attacking machine](post-exploitation/file-transfers.md)
* [Maintaining Access](post-exploitation/maintaining-access.md)
* [Pivoting](post-exploitation/pivoting.md)
* [Cleaning up](post-exploitation/cleanup.md)

## Reporting

* [How to report your findings](reporting/pentest-report.md)

## Red Team

* [Red Team](red-team/redteam.md)
* [Defenses Enumeration](red-team/AV-Enumeration.md)
* [AV Evasion](red-team/AV-Evasion-shellcode.md)

## Writeups

* [Hackthebox Tracks](writeups/HTBtracks.md)
  * [Hackthebox - Introduction to Android Exploitation - Track](writeups/HTB-Intro-to-android-Exploitation-Track.md)
* [Hackthebox Writeups](writeups/HTBwriteups.md)
  * [Hackthebox - Academy](/writeups/HTB-academy.md)
  * [Hackthebox - Access](writeups/HTB-access.md)
  * [Hackthebox - Active](/writeups/HTB-active.md)
  * [Hackthebox - Ambassador](/writeups/HTB-ambassador.md)
  * [Hackthebox - Arctic](/writeups/HTB-Arctic.md)
  * [Hackthebox - Awkward](/writeups/HTB-awkward.md)
  * [Hackthebox - Backend](writeups/HTB-Backend.md)
  * [Hackthebox - BackendTwo](/writeups/HTB-BackendTwo.md)
  * [Hackthebox - Bastard](/writeups/HTB-bastard.md)
  * [Hackthebox - Bastion](/writeups/HTB-Bastion.md)
  * [Hackthebox - Chatterbox](/writeups/HTB-chatterbox.md)
  * [Hackthebox - Devel](/writeups/HTB-Devel.md)
  * [Hackthebox - Driver](writeups/HTB-driver.md)
  * [Hackthebox - Explore](writeups/HTB-Explore.md)
  * [Hackthebox - Forest](writeups/HTB-forest.md)
  * [Hackthebox - Good games](writeups/HTB-goodgames.md)
  * [Hackthebox - Grandpa](writeups/HTB-grandpa.md)
  * [Hackthebox - Granny](writeups/HTB-granny.md)
  * [Hackthebox - Inject](writeups/HTB-inject.md)
  * [Hackthebox - Jeeves](writeups/HTB-Jeeves.md)
  * [Hackthebox - Jerry](writeups/HTB-Jerry.md)
  * [Hackthebox - Lame](writeups/HTB-Lame.md)
  * [Hackthebox - Late](writeups/HTB-Late.md)
  * [Hackthebox - Love](writeups/HTB-love.md)
  * [Hackthebox - Mentor](writeups/HTB-mentor.md)
  * [Hackthebox - MetaTwo](writeups/HTB-MetaTwo.md)
  * [Hackthebox - Monteverde](writeups/HTB-monteverde.md)
  * [Hackthebox - Nibbles](writeups/HTB-nibbles.md)
  * [Hackthebox - Optimum](writeups/HTB-optimum.md)
  * [Hackthebox - Paper](writeups/HTB-paper.md)
  * [Hackthebox - Photobomb](writeups/HTB-photobomb.md)
  * [Hackthebox - Poison](/writeups/HTB-poison.md)
  * [Hackthebox - Precious](writeups/HTB-Precious.md)
  * [Hackthebox - Querier](/writeups/HTB-querier.md)
  * [Hackthebox - Resolute](/writeups/HTB-Resolute.md)
  * [Hackthebox - RouterSpace](/writeups/HTB-RouterSpace.md)
  * [Hackthebox - Sauna](/writeups/HTB-Sauna.md)
  * [Hackthebox - SecNotes](writeups/HTB-secnotes.md)
  * [Hackthebox - Shoppy](writeups/HTB-Shoppy.md)
  * [Hackthebox - Soccer](writeups/HTB-Soccer.md)
  * [Hackthebox - Steamcloud](writeups/HTB-steamcloud.md)
  * [Hackthebox - Toolbox](writeups/HTB-toolbox.md)
  * [Hackthebox - Updown](writeups/HTB-updown.md)
* [TryHackme Writeups](writeups/THMwriteups.md)
  * [TryHackMe - Anonymous](writeups/THM-Anonymous.md)
  * [TryHackMe - Blaster](/writeups/THM-Blaster.md)
  * [TryHackMe - CMesS](writeups/THM-CMesS.md)
  * [TryHackMe - ConvertMyVideo](writeups/THM-convertmyvideo.md)
  * [TryHackMe - Corridor](writeups/THM-corridor.md)
  * [TryHackMe - LazyAdmin](writeups/THM-LazyAdmin.md)
  * [TryHackMe - Looking Glass](writeups/THM-looking-glass.md)
  * [TryHackMe - Nahamstore](writeups/THM-Nahamstore.md)
  * [TryHackMe - Overpass3](/writeups/THM-overpass3.md)
  * [TryHackMe - OWASP Top 10 2021](/writeups/THM-OWASP-Top-10-2021.md)
  * [TryHackMe - SimpleCTF](writeups/THM-SimpleCTF.md)
  * [TryHackMe - SQL Injection Lab](writeups/THM-SQLi-labs.md)
  * [TryHackMe - Sudo Security Bypass](writeups/THM-SudoSecurityBypass.md)
  * [TryHackMe - Tomghost](writeups/THM-tomghost.md)
  * [TryHackMe - Ultratech](/writeups/THM-UltraTech.md)
  * [TryHackMe - Vulnversity](/writeups/THM-vulnversity.md)
  * [TryHackMe - Wonderland](/writeups/THM-wonderland.md)
* [Vulnmachines Writeups](/writeups/vulnmachines-writeups.md)
  * [Web Labs Basic](/writeups/vulnmachine-web-basic.md)
  * [Web Labs Intermediate](writeups/vulnmachine-web-intermediate.md)
  * [Cloud Labs](/writeups/vulnmachines-cloud.md)
* [Mobile Hacking Lab](/writeups/MHLwriteups.md)
  * [Mobile Hacking Lab - Lab - Config Editor](/writeups/MHL-Lab-config.md)
* [Portswigger Web Security Academy Writeups](writeups/PSwriteups.md)
  * [PS - DomXSS](writeups/PS-DOMXSS.md)
* [OWASP projects and challenges writeups](writeups/OWASP-writeups.md)
  * [OWASP MAS Crackmes](writeups/OWASP-MAS-Crackmes.md)
* [Vulnerable APIs](writeups/vulnerable-apis.md)
  * [Vampi](writeups/VA-vampi.md)
  * [Damn Vulnerable Web Service](writeups/VA-dvws-node.md)
* [Various Platforms](writeups/various.md)
  * [flAWS 1&2](writeups/various-flaws.md)

## Digital skills

* [How to make a gitbook](/digital-skills/howto-gitbook.md)
* [Marp](/digital-skills/marp.md)
* [Linux Tips](digital-skills/useful-linux.md)
* [Docker](digital-skills/docker.md)
* [VSCodium](/digital-skills/vscodium.md)
* [Git Tips](/digital-skills/git-tips.md)
* [Obsidian](/digital-skills/obsidian.md)

## Durable skills

* [Durable skills wheel/Roue des compétences durables](durable-skills/wheel.md)

## Projects

* [Projects](projects/intro.md)
  * [Technical Projects](projects/technical-projects.md)
  * [General Projects](projects/general-projects.md)

## Talks

* [My Talks about Web Pentest](talks/web-talks.md)
* [My talks about Android Application hacking](talks/android-app.md)
* [Other of my talks and Podcast](talks/other-talks-podcasts.md)

## Resources

* [A list of random resources](resources.md)

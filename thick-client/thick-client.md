# Thick client

> This documentation is mostly made from notes of my practice. I then completed it with the Chapter "Attacking Thick Client Applications" from the [HTB Academy](https://academy.hackthebox.com) Module called "Attacking Common Applications"

## Definition

A thick client, or a fat or rich client, is a software application or system architecture that relies on a substantial amount of processing power and resources on the client side, typically a user's computer or device. The following features characterize thick clients:

- Local Processing: Thick clients perform significant data processing and application logic on the user's device. They can function independently of a continuous internet connection, as they don't rely heavily on server-side processing.

- Rich User Interface: Thick clients often provide a rich and interactive user interface with features like responsive graphics, real-time updates, and advanced user experiences.

- High Resource Requirements: Because thick clients rely on local resources, they consume more memory, processing power, and storage space on the user's device than thin clients or web applications.

- Installation Required: Thick clients typically require installation on the user's device. Users need to download and install the software package before using the application.

- Updates May Require User Action: When updates or patches are released for thick client applications, users may need to download and install them manually. On the other hand, web applications can be updated centrally on the server side.

Examples of thick clients include:

- Desktop applications like Microsoft Office Suite, Adobe Creative Suite, and video games installed on a computer or gaming console.
- Some financial trading platforms that offer real-time data analysis and trading capabilities.
- Media editing software such as Adobe Photoshop or Adobe Premiere that require substantial processing power for tasks like photo or video editing.
- CAD (Computer-Aided Design) software used by engineers and designers for creating 3D models.

Thick clients can offer advantages such as improved performance, offline functionality, and a more responsive user interface.  
Still, they also come with challenges like the need for frequent updates, potential security vulnerabilities, and higher resource demands on the user's device.  
The choice between thick clients, thin clients, or web applications depends on the specific requirements and constraints of a given project or application.

> The following is a complement from HTB Academy

Thick client applications can be categorized into two-tier and three-tier architecture. In two-tier architecture, the application is installed locally on the computer and communicates directly with the database. In the three-tier architecture, applications are also installed locally on the computer, but in order to interact with the databases, they first communicate with an application server, usually using the HTTP/HTTPS protocol. In this case, the application server and the database might be located on the same network or over the internet. This is something that makes three-tier architecture more secure since attackers won't be able to communicate directly with the database.  

![Two Tier vs Three Tier](../.res/2023-09-09-15-12-33.png)

> Image from HTB Academy

Web-specific vulnerabilities like XSS, CSRF, and Clickjacking, do not apply to thick client applications. However, thick client applications are considered less secure than web applications with many attacks being applicable, including:

- Improper Error Handling.
- Hardcoded sensitive data.
- DLL Hijacking.
- Buffer Overflow.
- SQL Injection.
- Insecure Storage.
- Session Management.

## Methodology to pentest thick client

> Section made with HTB Academy

### Information Gathering

In this step, penetration testers have to identify the application architecture, the programming languages and frameworks that have been used, and understand how the application and the infrastructure work. They should also need to identify technologies that are used on the client and server sides and find entry points and user inputs. Testers should also look for identifying common vulnerabilities

#### Information Gathering - Tools

- [CFF Explorer](https://ntcore.com/?page_id=388)
- [Detect It Easy](https://github.com/horsicq/Detect-It-Easy)
- [Process Monitor (Procmon)](https://learn.microsoft.com/en-us/sysinternals/downloads/procmon)
- [Strings](https://learn.microsoft.com/en-us/sysinternals/downloads/strings)

### Client Side Attacks

Although thick clients perform significant processing and data storage on the client side, they still communicate with servers for various tasks, such as data synchronization or accessing shared resources. This interaction with servers and other external systems can expose thick clients to vulnerabilities similar to those found in web applications, including command injection, weak access control, and SQL injection.

Sensitive information like usernames and passwords, tokens, or strings for communication with other services, might be stored in the application's local files. Hardcoded credentials and other sensitive information can also be found in the application's source code, thus Static Analysis is a necessary step while testing the application. Using the proper tools, we can reverse-engineer and examine .NET and Java applications including EXE, DLL, JAR, CLASS, WAR, and other file formats. Dynamic analysis should also be performed in this step, as thick client applications store sensitive information in the memory as well.

#### Client Side Attacks - Tools

- [Ghidra](https://www.ghidra-sre.org/)
- [IDA](https://hex-rays.com/ida-pro/)
- [OllyDbg](http://www.ollydbg.de/)
- [Radare2](https://www.radare.org/r/index.html)
- [dnSpy](https://github.com/dnSpy/dnSpy)
- [x64dbg](https://x64dbg.com/)
- [JADX](https://github.com/skylot/jadx)
- [Frida](https://frida.re/)

### Network Side Attacks

If the application is communicating with a local or remote server, network traffic analysis will help us capture sensitive information that might be transferred through HTTP/HTTPS or TCP/UDP connection, and give us a better understanding of how that application is working.

#### Network Side Attacks - Tools

- [Wireshark](https://www.wireshark.org/)
- [tcpdump](https://www.tcpdump.org/)
- [TCPView](https://learn.microsoft.com/en-us/sysinternals/downloads/tcpview)
- [Burp Suite](https://portswigger.net/burp)

### Server Side Attacks

Server-side attacks in thick client applications are similar to web application attacks, and penetration testers should pay attention to the most common ones including most of the OWASP Top Ten.

## Tips

### General tips

> Thanks to my colleague Cristian for these pointers :)

- Try to find an app binary and decompile it
- Play with tools like procmon, wireshark, burp mitm and echo mirage
- Check what controls are implemented on the client side
- Check the traffic.
- Can you bypass authentication

### If you can not be admin on the serv where the app is

> Thanks to my colleague Samuel for the following points on this part

- Copy the app in a VM you can control
- Make a tunnel between your machine and the server where the app is installed
- Test only in static and try to reproduce what you find on the server

### Find the binaries and access them or bypass access if protected

> Can happen in Citrix desktop for example

- Go to task manager and add details columns (right-click on "pid" and add columns), add command line columns or go to property to find the binary path.
- If you can't access the disk you want by typing "C:\" in the file browser, you can try accessing it by typing `\\127.0.0.1\c$` or `\\.\c$`.
- If these options don't work, you can try accessing it from the command line. In Powershell you can just `cd \\127.0.0.1\c$`.

> More info about this [here](https://community.spiceworks.com/topic/1970180-how-to-prevent-access-to-127-0-0-1-c-or-localhost-c)

### Read binaries and other Strings operations

- In the same way, when testing binaries on a Citrix, you can also open the files with notepad to see if there are any hardcoded credentials. It can be useful when you can ot install third party app.

- On powershell, you can use Strings.exe from sysinternal available [here](http://live.sysinternals.com/strings.exe). Then you can launch `strings.exe yourbinary.exe` and look for sensitive info, you can also grep on specific strings with a command like this `strings.exe yourbinary.exe | findstr key` this will grep on "key"

### Network

If you can not use wireshark or echo mirage you can compare your network state when the app is launched and when it is not.  

- Procmon, filter on process name and enable only network view
- We can then complete this with wireshark

#### Does the app open ports

- `netstat -ano | findstr LISTENING`
- For more command refer to network enumeration [here](https://csbygb.gitbook.io/pentips/windows/powershell-cmd#network-enumeration) and play around

## Tips on specific exploits

### DLL Hijacking

DLL hijacking, in essence, involves a method of elevating privileges where a harmful DLL is placed within the trusted application's directory. Subsequently, this deceptive DLL loads alongside genuine DLLs when the application is executed, potentially serving as a means to establish a reverse shell, attain persistence, or accomplish command execution.

#### Tools for DLL Hijacking

- [Procmon (sysinternals)](https://learn.microsoft.com/en-us/sysinternals/downloads/procmon)
- [DLLSpy](https://github.com/cyberark/DLLSpy)

## Resources

### Blog and courses

#### Thick Client Pentest

- [Desktop Applications / Thick Clients - by Lisandre](https://lisandre.com/pentest/desktop-applications)
- [Thick Client Pentest: Modern Approaches and Techniques: PART 1](https://infosecwriteups.com/thick-client-pentest-modern-approaches-and-techniques-part-1-7bb0f5f28e8e)
- [Introduction to Hacking Thick Clients: Part 1 – the GUI by Austin Altmann](https://www.netspi.com/blog/technical/thick-application-penetration-testing/introduction-to-hacking-thick-clients-part-1-the-gui/)
- [Introduction to Hacking Thick Clients: Part 2 – The Network by Austin Altmann](https://www.netspi.com/blog/technical/thick-application-penetration-testing/introduction-to-hacking-thick-clients-part-2-the-network/)
- [Introduction to Hacking Thick Clients: Part 3 – The File System and Registry by Austin Altmann](https://www.netspi.com/blog/technical/thick-application-penetration-testing/introduction-to-hacking-thick-clients-part-3/)
- [Introduction to Hacking Thick Clients: Part 4 – The Assemblies by Austin Altmann](https://www.netspi.com/blog/technical/thick-application-penetration-testing/introduction-to-hacking-thick-clients-part-4-the-assemblies/)
- [Introduction to Hacking Thick Clients: Part 5 – The API by Austin Altmann](https://www.netspi.com/blog/technical/thick-application-penetration-testing/introduction-to-hacking-thick-clients-part-5-the-api/)
- [Introduction to Hacking Thick Clients: Part 6 – The Memory by Austin Altmann](https://www.netspi.com/blog/technical/thick-application-penetration-testing/introduction-to-hacking-thick-clients-part-6-the-memory/)
- [Attacking Thick Client Application by @j33n1k4](https://youtu.be/GeTkV1cWwRM?si=yhUv0D0rzY9iSf_F)
- [Thick Client Pentest - Cobalt Core Academy](https://www.cobalt.io/blog/cobalt-core-academy-thick-client-pentesting)
- [Thick Client Pentesting: Architecture, Tools and Testing Approaches - Payatu](https://payatu.com/blog/thick-client-penetration-testing/)
- [Thick Client Penetration Testing Methodology - Darkrelay](https://www.darkrelay.com/post/thick-client-penetration-testing)
- [Thick Client Application Security Testing - Optiv](https://www.optiv.com/insights/source-zero/blog/thick-client-application-security-testing)
- [Thick Client Pentesting Methodology - CyberArk](https://www.cyberark.com/resources/threat-research-blog/thick-client-penetration-testing-methodology)
- [OWASP KOLKATA MEETUP #2 - Thick Client Penetration Testing by Souvik Roy](https://youtu.be/V4RdppcosOs?si=4YHWqEiUFdU5XGoK)
- [Thick Client Security Assessment — I by SAKSHAM CHAWLA](https://medium.com/@chawla.saksham08/thick-client-security-assessment-i-bb02ac37e63)
- [Thick Client Security Assessment — II by SAKSHAM CHAWLA](https://medium.com/@chawla.saksham08/thick-client-security-assessment-ii-863526bfa88b)
- [Security Testing of Thick Client Application - David Valles](https://medium.com/@david.valles/security-testing-of-thick-client-application-15612f326cac)
- [Thick Client Pentesting by hexachordanu](https://github.com/hexachordanu/Thick-Client-Pentesting/tree/master)

#### Resources - DLL Hijacking

- [Have you ever hijacked a DLL?! - Michelle Eggers](https://www.linkedin.com/posts/m-eggers_cybersecurity-pentesting-penetrationtesting-activity-7102691799814610945-D0rl?utm_source=share&utm_medium=member_desktop)
- [All About DLL Hijacking - My Favorite Persistence Method - IppSec](https://youtu.be/3eROsG_WNpE?si=q2VoaY_1Sr-tNdA6)
- [Dll Hijacking - Hacktricks](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/dll-hijacking)

#### Resources - Linux Shared Library Hijacking

- [Linux Shared Library Hijacking by Xavi](https://xavibel.com/2022/09/06/linux-shared-library-hijacking/)

#### Other related topics

- [Echo Mirage - Walkthrough on Infosec Institute by Chiragh Dewan](https://resources.infosecinstitute.com/topics/network-security-101/echo-mirage-walkthrough/)

### Checklists and Minmaps

- [Thick Client Pentest - Checklist by Hari Prasaanth](https://github.com/Hari-prasaanth/Thick-Client-Pentest-Checklist)
- [Thick Client Pentesting mindmap by Security Boat](https://workbook.securityboat.in/mindmaps/thick-client-pentest-mindmap)

### Labs to practice

- [BetaFast by NetSPI- vulnerable thick client to practice on](https://github.com/NetSPI/BetaFast)
- [DVTA 2.0](https://github.com/srini0x00/dvta)
  - [Setting up DVTA - Part 1 by Source Meets Sink](https://youtu.be/rx8mtI1HU5c?si=GW_lo3J2RsUvSjVw)
  - [Setting up DVTA - Part 2 by Source Meets Sink](https://youtu.be/IBdk2uOessc?si=SHCYnI4CYzRwIuvT)
- [Terrible-Thick-CLient by Kartikdurg](https://github.com/kartikdurg/Terrible-Thick-Client)
- [Another Vulnerable Thickclient by diljith369](https://github.com/diljith369/AVT)
- [It is a Broken App (IBA)](https://github.com/diljith369/IBA)

### Writeups

- [Thick Client Application Pen-Testing Lab | DVTA (Damn Vulnerable Thick Client Application) | Part-1 by Chaitanya Eshwar Prasad](https://youtu.be/6zFhuoPj8JE?si=LawN29MakqGg4Nv2)
- [Thick Client Application Pen-Testing Lab | DVTA (Damn Vulnerable Thick Client Application) | Part-2 by Chaitanya Eshwar Prasad](https://youtu.be/Cb-K8gB8pLo?si=TpOmE5rgYPScBQ_t)
- [Thick Client Application Pen-Testing Lab | DVTA (Damn Vulnerable Thick Client Application) | Part-3 by Chaitanya Eshwar Prasad](https://youtu.be/SbqJU1PcsE8?si=KDODm86bO0RZ8vre)
- [🎯 Thick Client Penetration Testing: Hands-on Practice with AVT (Another Vulnerable Thick Client) 🚀💻 by aalphaas](https://youtu.be/LCrPmtY83IA?si=eudMtRQiCxOnVRF5)
- [🎯 Thick Client Penetration Testing: Hands-on Practice Part 2 with IBA (It is a Broken App) by aalphaas](https://youtu.be/cCChPFkq4cY?si=XGUH5EccjOoUJtYg)
# Thick client

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

## Tips

### General tips

> Thanks to my colleague Cristian for these pointers :)

- Try to find an app binary and decompile it
- Play with tools like procmon, wireshark, burp mitm and echo mirage
- Check what controls are implemented on the client side
- Check the traffic.
- Can you bypass authentication

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
- [Thick Client Pentest - Cobalt Core Academy](https://www.cobalt.io/blog/cobalt-core-academy-thick-client-pentesting)
- [Thick Client Pentesting: Architecture, Tools and Testing Approaches - Payatu](https://payatu.com/blog/thick-client-penetration-testing/)
- [Thick Client Penetration Testing Methodology - Darkrelay](https://www.darkrelay.com/post/thick-client-penetration-testing)
- [Thick Client Application Security Testing - Optiv](https://www.optiv.com/insights/source-zero/blog/thick-client-application-security-testing)
- [Thick Client Pentesting Methodology - CyberArk](https://www.cyberark.com/resources/threat-research-blog/thick-client-penetration-testing-methodology)

#### Other related topics

- [Echo Mirage - Walkthrough on Infosec Institute by Chiragh Dewan](https://resources.infosecinstitute.com/topics/network-security-101/echo-mirage-walkthrough/)

### Checklists

- [Thick Client Pentest - Checklist by Hari Prasaanth](https://github.com/Hari-prasaanth/Thick-Client-Pentest-Checklist)

### Labs to practice

- [BetaFast by NetSPI- vulnerable thick client to practice on](https://github.com/NetSPI/BetaFast)
- [DVTA 2.0](https://github.com/srini0x00/dvta)
- [Setting up DVTA - Part 1 by Source Meets Sink](https://youtu.be/rx8mtI1HU5c?si=GW_lo3J2RsUvSjVw)
- [Setting up DVTA - Part 2 by Source Meets Sink](https://youtu.be/IBdk2uOessc?si=SHCYnI4CYzRwIuvT)
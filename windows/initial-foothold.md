# Gaining an Initial foothold

> Documentation made from my notes and [HTB Academy's](https://academy.hackthebox.com) module on shells & payloads

## Enumeration

### Nmap

Launch an nmap scan, my favorite is:  

- `sudo nmap -T4 -sC -sV -p- --min-rate=1000 IP-ADR`  
  The `--min-rate` is so that the packets are sent faster than what is specified, `-sC` is for the scripts, it will use default scripts for enum and `-sV` is for services, `-p-` specifies we want all ports not only the top ones.

- `sudo nmap -T4 -sC -sV -O -Pn -p- IP-ADR`

## General tips about Windows

### Payloads types to Consider

- [DLLs](https://docs.microsoft.com/en-us/troubleshoot/windows-client/deployment/dynamic-link-library) A Dynamic Linking Library (DLL) is a library file used in Microsoft operating systems to provide shared code and data that can be used by many different programs at once. These files are modular and allow us to have applications that are more dynamic and easier to update. As a pentester, injecting a malicious DLL or hijacking a vulnerable library on the host can elevate our privileges to SYSTEM and/or bypass User Account Controls.

- [Batch](https://commandwindows.com/batch.htm) Batch files are text-based DOS scripts utilized by system administrators to complete multiple tasks through the command-line interpreter. These files end with an extension of .bat. We can use batch files to run commands on the host in an automated fashion. For example, we can have a batch file open a port on the host, or connect back to our attacking box. Once that is done, it can then perform basic enumeration steps and feed us info back over the open port.

- [VBS](https://www.guru99.com/introduction-to-vbscript.html) VBScript is a lightweight scripting language based on Microsoft's Visual Basic. It is typically used as a client-side scripting language in webservers to enable dynamic web pages. VBS is dated and disabled by most modern web browsers but lives on in the context of Phishing and other attacks aimed at having users perform an action such as enabling the loading of Macros in an excel document or clicking on a cell to have the Windows scripting engine execute a piece of code.

- [MSI](https://docs.microsoft.com/en-us/windows/win32/msi/windows-installer-file-extensions) .MSI files serve as an installation database for the Windows Installer. When attempting to install a new application, the installer will look for the .msi file to understand all of the components required and how to find them. We can use the Windows Installer by crafting a payload as an .msi file. Once we have it on the host, we can run msiexec to execute our file, which will provide us with further access, such as an elevated reverse shell.

- [Powershell](https://docs.microsoft.com/en-us/powershell/scripting/overview?view=powershell-7.1) Powershell is both a shell environment and scripting language. It serves as Microsoft's modern shell environment in their operating systems. As a scripting language, it is a dynamic language based on the .NET Common Language Runtime that, like its shell component, takes input and output as .NET objects. PowerShell can provide us with a plethora of options when it comes to gaining a shell and execution on a host, among many other steps in our penetration testing process.

### Payload Transfer and Execution

- Impacket: [Impacket](https://github.com/SecureAuthCorp/impacket) is a toolset built-in Python that provides us a way to interact with network protocols directly. Some of the most exciting tools we care about in Impacket deal with psexec, smbclient, wmi, Kerberos, and the ability to stand up an SMB server.
- [Payloads All The Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Download%20and%20Execute.md): is a great resource to find quick oneliners to help transfer files across hosts expediently.
- SMB: SMB can provide an easy to exploit route to transfer files between hosts. This can be especially useful when the victim hosts are domain joined and utilize shares to host data. We, as attackers, can use these SMB file shares along with C$ and admin$ to host and transfer our payloads and even exfiltrate data over the links.
- Remote execution via MSF: Built into many of the exploit modules in Metasploit is a function that will build, stage, and execute the payloads automatically.
- Other Protocols: When looking at a host, protocols such as FTP, TFTP, HTTP/S, and more can provide you with a way to upload files to the host. Enumerate and pay attention to the functions that are open and available for use.

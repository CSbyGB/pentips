# Payload tools

> This documentation is made from my notes of [Hackthebox Academy's module Shell & Payloads](https://academy.hackthebox.com/)

## Payload Generation

|Resource|Description|
|--------|-----------|
|[MSFVenom & Metasploit-Framework](https://github.com/rapid7/metasploit-framework)| MSF is an extremely versatile tool for any pentester's toolkit. It serves as a way to enumerate hosts, generate payloads, utilize public and custom exploits, and perform post-exploitation actions once on the host. Think of it as a swiss-army knife.|
|[Payloads All The Things](https://github.com/swisskyrepo/PayloadsAllTheThings)|Source Here, you can find many different resources and cheat sheets for payload generation and general methodology.|
|[Mythic C2 Framework](https://github.com/its-a-feature/Mythic)|Source The Mythic C2 framework is an alternative option to Metasploit as a Command and Control Framework and toolbox for unique payload generation.|
|[Nishang](https://github.com/samratashok/nishang) |Source Nishang is a framework collection of Offensive PowerShell implants and scripts. It includes many utilities that can be useful to any pentester.|
|[Darkarmour](https://github.com/bats3c/darkarmour)|Source Darkarmour is a tool to generate and utilize obfuscated binaries for use against Windows hosts.|

## Payload Transfer and Execution

- [Impacket](https://github.com/SecureAuthCorp/impacket): Impacket is a toolset built-in Python that provides us a way to interact with network protocols directly. Some of the most exciting tools we care about in Impacket deal with psexec, smbclient, wmi, Kerberos, and the ability to stand up an SMB server.
- [Payloads All The Things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Download%20and%20Execute.md): is a great resource to find quick oneliners to help transfer files across hosts expediently.
- SMB: SMB can provide an easy to exploit route to transfer files between hosts. This can be especially useful when the victim hosts are domain joined and utilize shares to host data. We, as attackers, can use these SMB file shares along with C$ and admin$ to host and transfer our payloads and even exfiltrate data over the links.
- Remote execution via MSF: Built into many of the exploit modules in Metasploit is a function that will build, stage, and execute the payloads automatically.
- Other Protocols: When looking at a host, protocols such as FTP, TFTP, HTTP/S, and more can provide you with a way to upload files to the host. Enumerate and pay attention to the functions that are open and available for use.

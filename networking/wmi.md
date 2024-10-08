# WMI

> *Source CTF and HTB Academy*

- Usually on port 135

Windows Management Instrumentation (WMI) is Microsoft's implementation and also an extension of the Common Information Model (CIM), core functionality of the standardized Web-Based Enterprise Management (WBEM) for the Windows platform. WMI allows read and write access to almost all settings on Windows systems. Understandably, this makes it the most critical interface in the Windows environment for the administration and remote maintenance of Windows computers, regardless of whether they are PCs or servers. WMI is typically accessed via PowerShell, VBScript, or the Windows Management Instrumentation Console (WMIC). WMI is not a single program but consists of several programs and various databases, also known as repositories.  

## Enumeration

### Impacket - WMIexec

- `impacket-wmiexec Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"` connect

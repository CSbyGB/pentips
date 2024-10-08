# Defenses Enumeration

## Host Security Solutions

It is a set of software applications used to monitor and detect abnormal and malicious activities within the host, including:

- Antivirus software
- Microsoft Windows Defender
- Host-based Firewall
- Security Event Logging and Monitoring 
- Host-based Intrusion Detection System (HIDS)/ Host-based Intrusion - - Prevention System (HIPS)
- Endpoint Detection and Response (EDR)

### Antivirus Software (AV)

Antivirus software also known as anti-malware, is mainly used to monitor, detect, and prevent malicious software from being executed within the host.

**Signature-based detection** is one of the common and traditional techniques used in antivirus software to identify malicious files. Often, researchers or users submit their infected files into an antivirus engine platform for further analysis by AV vendors, and if it confirms as malicious, then the signature gets registered in their database. The antivirus software compares the scanned file with a database of known signatures for possible attacks and malware on the client-side. If we have a match, then it considers a threat.

**Heuristic-based detection** uses machine learning to decide whether we have the malicious file or not. It scans and statically analyses in real-time in order to find suspicious properties in the application's code or check whether it uses uncommon Windows or system APIs. It does not rely on the signature-based attack in making the decisions, or sometimes it does. This depends on the implementation of the antivirus software.

**Behavior-based detection** relies on monitoring and examining the execution of applications to find abnormal behaviors and uncommon activities, such as creating/updating values in registry keys, killing/creating processes, etc.

### Microsoft Windows Defender

Microsoft Windows Defender is a pre-installed antivirus security tool that runs on endpoints.  
Active mode is used where the MS Defender runs as the primary antivirus software on the machine where provides protection and remediation. Passive mode is run when a 3rd party antivirus software is installed. Therefore, it works as secondary antivirus software where it scans files and detects threats but does not provide remediation. Finally, Disable mode is when the MS Defender is disabled or uninstalled from the system.

### Host-based Firewall

It is a security tool installed and run on a host machine that can prevent and block attacker or red teamers' attack attempts. Thus, it is essential to enumerate and gather details about the firewall and its rules within the machine we have initial access to.  

### Sysmon

[Windows System Monitor sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon) is a service and device driver. It is one of the Microsoft Sysinternals suites. The sysmon tool is not an essential tool (not installed by default), but it starts gathering and logging events once installed. These logs indicators can significantly help system administrators and blue teamers to track and investigate malicious activity and help with general troubleshooting.

### Host-based Intrusion Detection/Prevention System (HIDS/HIPS)

HIDS stands for Host-based Intrusion Detection System. It is software that has the ability to monitor and detect abnormal and malicious activities in a host. The primary purpose of HIDS is to detect suspicious activities and not to prevent them. There are two methods that the host-based or network intrusion detection system works, including:

- Signature-based IDS - it looks at checksums and message authentication.
- Anomaly-based IDS looks for unexpected activities, including abnormal bandwidth usage, protocols, and ports.

### Endpoint Detection and Response (EDR)

It is also known as Endpoint Detection and Threat Response (EDTR). The EDR is a cybersecurity solution that defends against malware and other threats. EDRs can look for malicious files, monitor endpoint, system, and network events, and record them in a database for further analysis, detection, and investigation. EDRs are the next generation of antivirus and detect malicious activities on the host in real-time.

### Enumeration

- `wmic /namespace:\\root\securitycenter2 path antivirusproduct` Enumerate AV software
- `Get-CimInstance -Namespace root/SecurityCenter2 -ClassName AntivirusProduct` Enumerate AV software using powershell
- `Get-Service WinDefend` check the service state of Windows Defender
- `Get-MpComputerStatus ` provides the current status of security solution elements, including Anti-Spyware, Antivirus, LoavProtection, Real-time protection, etc
- `Get-MpComputerStatus | select RealTimeProtectionEnabled` check specifically for real time protection
- `Get-NetFirewallProfile | Format-Table Name, Enabled` check if firewall is enabled
- `Get-NetFirewallRule | select DisplayName, Enabled, Description`
- `Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled False` Disable firewall (requires admin privileges)
- `Test-NetConnection -ComputerName 127.0.0.1 -Port 80` check if the inbound connection on port 80 is open and allowed in the firewall
- `Get-MpThreat` threats details that have been detected using MS Defender
- `Get-EventLog -List` get a list of available event logs on the local machine
- `Get-Process | Where-Object { $_.ProcessName -eq "Sysmon" }` look for a process or service that has been named "Sysmon" within the current process or services
- `Get-CimInstance win32_service -Filter "Description = 'System Monitor service'"` or `Get-Service | where-object {$_.DisplayName -like "*sysm*"}` look for services (sysmon)
- `reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\WINEVT\Channels\Microsoft-Windows-Sysmon/Operational` check the Windows registry for sysmon
- `findstr /si '<ProcessCreate onmatch="exclude">' C:\tools\*` find the sysmon configuration file
- Scripts to enumerate Antivirus, EDR and logging monitor products:
  - [Invoke-EDRChecker](https://github.com/PwnDexter/Invoke-EDRChecker)
  - [SharpEDRChecker](https://github.com/PwnDexter/SharpEDRChecker)

## Network Security Solutions

### Network Firewall

A firewall is the first checkpoint for untrusted traffic that arrives at a network. The firewall filters the untrusted traffic before passing it into the network based on rules and policies. In addition, Firewalls can be used to separate networks from external traffic sources, internal traffic sources, or even specific applications. Nowadays, firewall products are built-in network routers or other security products that provide various security features. The following are some firewall types that enterprises may use.

- Packet-filtering firewalls
- Proxy firewalls
- NAT firewalls
- Web application firewalls

### Security Information and Event Management (SIEM)

SIEM combines Security Information Management (SIM) and Security Event Management (SEM) to monitor and analyze events and track and log data in real-time. SIEM helps system administrators and blue teamers to monitor and track potential security threats and vulnerabilities before causing damage to an organization.  
SIEM solutions work as log data aggregation center, where it collects log files from sensors and perform functions on the gathered data to identify and detect security threats or attacks.

### Intrusion Detection System and Intrusion Prevention System (NIDS/NIPS)

Network-based IDS/IPS have a similar concept to the host-based IDS/IPS. The main difference is that the network-based products focus on the security of a network instead of a host. The network-based solution will be based on sensors and agents distributed in the network devices and hosts to collect data. IDS and IPS are both detection and monitoring cybersecurity solutions that an enterprise uses to secure its internal systems. They both read network packets looking for abnormal behaviors and known threats pre-loaded into a previous database. The significant difference between both solutions is that the IDS requires human interaction or 3rd party software to analyze the data to take action. The IPS is a control system that accepts or rejects packets based on policies and rules.  

Common enterprise IDS/IPS products: Palo Alto Networks, Cisco's Next-Generation, McAfee Network Security Platform (NSP), Trend Micro TippingPoint, Suricata.

## Enumerate Applications and Service

- `wmic product get name,version` list all installed applications and their version.
- `net start` list running services
- `wmic service where "name like 'THM Demo'" get Name,PathName` get more info on the service named THM Demo
- `Get-Process -Name thm-demo` get more details on the executable related to the service in our case thm-demo.exe. This way we get a process ID
- `netstat -noa |findstr "LISTENING" |findstr "3212"` see if the process is a network service by listing listening ports.

- `nslookup.exe` Launch the tool to perform DNS zone transfer
- `server 10.10.30.149` provide the DNS server

## Resources

{% embed url="https://tryhackme.com/room/thelayoftheland" %} Tryhackme - The lay of the the Land {% endembed %}  

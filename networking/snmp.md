# SNMP

> *Source CTF and HTB Academy*

- Usually on port UDP 161 and UDP 162

Simple Network Management Protocol (SNMP) was created to monitor network devices. In addition, this protocol can also be used to handle configuration tasks and change settings remotely.  
SNMP Community strings provide information and statistics about a router or device, helping us gain access to it. The manufacturer default community strings of public and private are often unchanged. In SNMP versions 1 and 2c, access is controlled using a plaintext community string, and if we know the name, we can gain access to it. Encryption and authentication were only added in SNMP version 3. Much information can be gained from SNMP.  
Examination of process parameters might reveal credentials passed on the command line, which might be possible to reuse for other externally accessible services given the prevalence of password reuse in enterprise environments. Routing information, services bound to additional interfaces, and the version of installed software can also be revealed  

## MIB

To ensure that SNMP access works across manufacturers and with different client-server combinations, the Management Information Base (MIB) was created. MIB is an independent format for storing device information. A MIB is a text file in which all queryable SNMP objects of a device are listed in a standardized tree hierarchy. It contains at least one Object Identifier (OID), which, in addition to the necessary unique address and a name, also provides information about the type, access rights, and a description of the respective object. MIB files are written in the Abstract Syntax Notation One (ASN.1) based ASCII text format. The MIBs do not contain data, but they explain where to find which information and what it looks like, which returns values for the specific OID, or which data type is used.

## OID

An OID represents a node in a hierarchical namespace. A sequence of numbers uniquely identifies each node, allowing the node's position in the tree to be determined. The longer the chain, the more specific the information. Many nodes in the OID tree contain nothing except references to those below them. The OIDs consist of integers and are usually concatenated by dot notation. We can look up many MIBs for the associated OIDs in the Object Identifier Registry.

### SNMPv1

SNMP version 1 (SNMPv1) is used for network management and monitoring. SNMPv1 is the first version of the protocol and is still in use in many small networks. It supports the retrieval of information from network devices, allows for the configuration of devices, and provides traps, which are notifications of events. However, SNMPv1 has no built-in authentication mechanism, meaning anyone accessing the network can read and modify network data. Another main flaw of SNMPv1 is that it does not support encryption, meaning that all data is sent in plain text and can be easily intercepted.

### SNMPv2

SNMPv2 existed in different versions. The version still exists today is v2c, and the extension c means community-based SNMP. Regarding security, SNMPv2 is on par with SNMPv1 and has been extended with additional functions from the party-based SNMP no longer in use. However, a significant problem with the initial execution of the SNMP protocol is that the community string that provides security is only transmitted in plain text, meaning it has no built-in encryption.

### SNMPv3

The security has been increased enormously for SNMPv3 by security features such as authentication using username and password and transmission encryption (via pre-shared key) of the data. However, the complexity also increases to the same extent, with significantly more configuration options than v2c.

### Community Strings

Community strings can be seen as passwords that are used to determine whether the requested information can be viewed or not. It is important to note that many organizations are still using SNMPv2, as the transition to SNMPv3 can be very complex, but the services still need to remain active. This causes many administrators a great deal of concern and creates some problems they are keen to avoid. The lack of knowledge about how the information can be obtained and how we as attackers use it makes the administrators' approach seem inexplicable. At the same time, the lack of encryption of the data sent is also a problem. Because every time the community strings are sent over the network, they can be intercepted and read.

## Default Configuration

- `cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'`

## Dangerous Settings

|Settings|Description|
|--------|-----------|
|`rwuser noauth`|Provides access to the full OID tree without authentication.|
|`rwcommunity <community string> <IPv4 address>`|Provides access to the full OID tree regardless of where the requests were sent from.|
|`rwcommunity6 <community string> <IPv6 address>`|Same access as with rwcommunity with the difference of using IPv6.|

## Enumeration

### Nmap

- `sudo nmap -sU -sV -p 161 -Pn -n --disable-arp-ping --script=snmp-* 10.129.201.223`

> In general, Nmap is not always 100% accurate, it can happen that it mentions snmp to be v3 when it might be v2c.

### snmpwalk

- `sudo perl /usr/share/doc/libnet-snmp-perl/examples/snmpwalk.pl -c public 10.129.220.234`
- `snmpwalk -v2c -c public 10.129.14.128`
- `snmpwalk -v1 -c public 10.10.1.224 .1.3.6.1.4.1.318` the `.1.3.6.1.4.1.318`, is the OID
- See more about snmpwalk [here](https://www.comparitech.com/net-admin/snmpwalk-examples-windows-linux/)
- `snmpwalk -v 2c -c private 10.10.1.224`

### onesixtyone

- [Github repo](https://github.com/trailofbits/onesixtyone)
- `sudo apt install onesixtyone`
- `onesixtyone -c dict.txt 10.129.42.254`
- A tool such as onesixtyone can be used to brute force the community string names using a dictionary file of common community strings such as the dict.txt file included in the GitHub repo for the tool.

### Braa

- [Github Repo](https://github.com/mteg/braa)
- `sudo apt install braa`
- `braa <community string>@<IP>:.1.3.6.*` example `braa public@10.129.14.128:.1.3.6.*`

## Example

- We enumerate our target with onesixtyone and get a community string this way

```bash
┌─[✗]─[gabrielle@parrot]─[~]
└──╼ $onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt 10.129.206.8
Scanning 1 hosts, 3220 communities
10.129.206.8 [backup] Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64
```

- `backup` is the community string
- Using this information we can run snmpwalk as follows: `snmpwalk -v 2c -c private 10.10.1.224`

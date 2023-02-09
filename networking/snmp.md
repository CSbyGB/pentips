# SNMP

SNMP Community strings provide information and statistics about a router or device, helping us gain access to it. The manufacturer default community strings of public and private are often unchanged. In SNMP versions 1 and 2c, access is controlled using a plaintext community string, and if we know the name, we can gain access to it. Encryption and authentication were only added in SNMP version 3. Much information can be gained from SNMP.  
Examination of process parameters might reveal credentials passed on the command line, which might be possible to reuse for other externally accessible services given the prevalence of password reuse in enterprise environments. Routing information, services bound to additional interfaces, and the version of installed software can also be revealed  
*Source Hackthebox Academy*

## snmpwalk

- `snmpwalk -v1 -c public 10.10.1.224 .1.3.6.1.4.1.318` the `.1.3.6.1.4.1.318`, is the OID
- See more about snmpwalk [here](https://www.comparitech.com/net-admin/snmpwalk-examples-windows-linux/)
- `snmpwalk -v 2c -c private 10.10.1.224`

## onesixtyone

- [Github repo](https://github.com/trailofbits/onesixtyone)
- `onesixtyone -c dict.txt 10.129.42.254`
- A tool such as onesixtyone can be used to brute force the community string names using a dictionary file of common community strings such as the dict.txt file included in the GitHub repo for the tool.
*Source Hackthebox Academy*
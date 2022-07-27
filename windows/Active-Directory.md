# Active Directory

*Note: This documentation is mostly made from my notes on [TCM Security Academy](https://academy.tcm-sec.com/)  
It will be complemented with notes from my practice and from other classes like the one from [HTB Academy](https://academy.hackthebox.com/)*

## Physical Components

### Domain Controllers

![Domain Controllers](../.res/domain-controllers.png)

### AD DS Data Store

When compromising AD we want to grab this file, it contains everything users objects groups password hashes for all the users etc…  
![AD DS Data Store](../.res/adds-datastore.png)

## Logical Components

Domain => Tree => Forest

### AD DS Schema

![AD DS Data Schema](../.res/adds-schema.png)

### Domains

![Domains](../.res/domains.png)

### Trees

![Trees](../.res/trees.png)

### Forests

![Forests](../.res/forests.png)

### Organizational Units (Ous)

![Organizational Units (Ous)](../.res/ous.png)

### Trusts

![Trusts](../.res/trusts.png)

### Objects

![Objects](../.res/objects.png)  

- Domain are used to manage objects in an org
- If multiple domain, we have a tree
- If multiple set of trees we have a forest
- Across forest or across domain we have trust
  
- Here is a very comprehensive Schema from HTB Academy  
![image](https://user-images.githubusercontent.com/96747355/167955114-990a094c-ca46-4326-b281-ccf92a20f992.png)  

### Rights and privileges in AD

| Group | Description|
|-------|------------|
|Default Administrators|Domain Admins and Enterprise Admins "super" groups.|
|Server Operators|Members can modify services, access SMB shares, and backup files.|
|Backup Operators|Members are allowed to log onto DCs locally and should be considered Domain Admins. They can make shadow copies of the SAM/NTDS database, read the registry remotely, and access the file system on the DC via SMB. This group is sometimes added to the local Backup Operators group on non-DCs.|
|Print Operators|Members are allowed to logon to DCs locally and "trick" Windows into loading a malicious driver.|
|Hyper-V Administrators|If there are virtual DCs, any virtualization admins, such as members of Hyper-V Administrators, should be considered Domain Admins.|
|Account Operators|Members can modify non-protected accounts and groups in the domain.|
|Remote Desktop Users|Members are not given any useful permissions by default but are often granted additional rights such as Allow Login Through Remote Desktop Services and can move laterally using the RDP protocol.|
|Remote Management Users|Members are allowed to logon to DCs with PSRemoting (This group is sometimes added to the local remote management group on non-DCs).|
|Group Policy Creator Owners|Members can create new GPOs but would need to be delegated additional permissions to link GPOs to a container such as a domain or OU.|
|Schema Admins|Members can modify the Active Directory schema structure and can backdoor any to-be-created Group/GPO by adding a compromised account to the default object ACL.|
|DNS Admins|Members have the ability to load a DLL on a DC but do not have the necessary permissions to restart the DNS server. They can load a malicious DLL and wait for a reboot as a persistence mechanism. Loading a DLL will often result in the service crashing. A more reliable way to exploit this group is to create a WPAD record.|

### LDAP Overview

![image](https://user-images.githubusercontent.com/96747355/167956004-99711e9c-98ac-4825-a12a-2f038260fd7e.png)  

The relationship between AD and LDAP can be compared to Apache and HTTP. The same way Apache is a web server that uses the HTTP protocol, Active Directory is a directory server that uses the LDAP protocol.  
While uncommon, you may come across organizations while performing an assessment that does not have AD but does have LDAP, meaning that they most likely use another type of LDAP server such as [OpenLDAP](https://en.wikipedia.org/wiki/OpenLDAP).  

#### LDAP authentication

![Ldap auth](../.res/2022-07-27-16-32-14.png)  

Source: [Tryhackme](https://tryhackme.com/room/breachingad)

#### LDAP Pass-back Attacks

This is a common attack against network devices, such as printers, when you have gained initial access to the internal network, such as plugging in a rogue device in a boardroom.

LDAP Pass-back attacks can be performed when we gain access to a device's configuration where the LDAP parameters are specified. This can be, for example, the web interface of a network printer. Usually, the credentials for these interfaces are kept to the default ones, such as admin:admin or admin:password. Here, we won't be able to directly extract the LDAP credentials since the password is usually hidden. However, we can alter the LDAP configuration, such as the IP or hostname of the LDAP server. In an LDAP Pass-back attack, we can modify this IP to our IP and then test the LDAP configuration, which will force the device to attempt LDAP authentication to our rogue device. We can intercept this authentication attempt to recover the LDAP credentials.

Source: [Tryhackme](https://tryhackme.com/room/breachingad)

## NTLM and NetNTLM

NetNTLM, also often referred to as Windows Authentication or just NTLM Authentication, allows the application to play the role of a middle man between the client and AD. All authentication material is forwarded to a Domain Controller in the form of a challenge, and if completed successfully, the application will authenticate the user.

This means that the application is authenticating on behalf of the user and not authenticating the user directly on the application itself. This prevents the application from storing AD credentials, which should only be stored on a Domain Controller. This process is shown in the diagram below:  

![NTLM and NetNTLM](../.res/2022-07-27-16-33-00.png)

Source: [Tryhackme](https://tryhackme.com/room/breachingad)

## Resources

### Learn about AD

*Note: If you want a nice crash course about Active Directory (with the lab setup) I really recommend this one by TCM-Security Academy:*
{% embed url="https://academy.tcm-sec.com/p/practical-ethical-hacking-the-complete-course" %} TCM-Security Academy {% endembed %}
{% embed url="https://academy.hackthebox.com/course/preview/active-directory-ldap" %} HTB Academy - Active Directory LDAP {% endembed %}

### Make your own Practice lab

{% embed url="https://www.microsoft.com/en-us/evalcenter/" %} Microsoft Evaluation Center {% endembed %}
{% embed url="https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise" %} Windows 10 {% endembed %}
{% embed url="https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019" %} Windows Server 2019 {% endembed %}
*Note: To unlock Win Server 2019 and access the login screen from virtual box, the combinaison is ctrl+suppr*
{% embed url="https://www.kali.org/" %} Kali Linux {% endembed %}
{% embed url="https://tryhackme.com/room/breachingad" %}TryHackMe - Breaching AD {% endembed %}

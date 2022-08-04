# Powerview or SharpView (.NET equivalent)

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
- `Get-NetDomain` similar to the ActiveDirectory module’s Get-ADDomain but contains a lot less information. Basic info such as the Forest, Domain Controllers, and Domain Name are enumerated.
- `Get-NetDomainController` list all of the Domain Controllers within the network
- `Get-NetForest` similar to Get-ADForest, and provides similar output. It provides all the associated Domains, the root domain, as well as the Domain Controllers for the root domain.
- `Get-NetDomainTrust` is similar to Get-ADTrust with our SelectObject filter applied to it.

## Enumerating Users

- `get-netuser` will output all infos about users in the domain
- `get-netuser | select cn` will list all users 
- `get-netuser | select -expandproperty samaccountname` will list users but only samccountname
- `find-userfield -SearchField description "password"` will list description fields of users with a grep on "password"

## Enumerating Groups

- `get-netgroup` will list all the different groups in the domain
- `get-netgroup -Username "f.lastname"` will show group of user f.lastname
- `get-netgroup -GroupName "domain admins" -FullData` will show details of the group

## Enumerating Domain Computers and Shares

- `Get-NetComputer -OperatingSystem "*Windows 10*"` Get computer with Win 10 OS
- `Get-NetComputer -OperatingSystem "*server*"` Get the server
- `Invoke-ShareFinder` will list shares
- `Invoke-ShareFinder -ExcludeStandard -ExcludePrint -ExcludeIPC` will list sharw without standard print and IPC

## Find interesting files

- `Invoke-FileFinder`

## Enumerate Local Admin Users

- `Invoke-EnumerateLocalAdmin`

## Enumerating Group Policy Objects

- `get-netgpo`

## Enumerating Access Control Lists

- `get-objectacl`
- `get-objectacl -SamAccountName "name" -ResolveGUIDs`

## Enumerating the domain

- `get-netdomain`
- `Get-DomainPolicy`
- `Get-domainsid` useful for golden tickets

*Note: If you do not get result with powerview, you can try this in powershell `Import-Module .\PowerView.ps1`*

## Powerview - Resources

- [PowerView Cheat Sheet](https://gist.github.com/HarmJ0y/184f9822b195c52dd50c379ed3117993)

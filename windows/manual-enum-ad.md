# AD Manual Enumeration

## Operators to use with Filter

- `-eq`	Equal to
- `-le`	Less than or equal to
- `-ge`	Greater than or equal to
- `-ne`	Not equal to
- `-lt`	Less than
- `-gt`	Greater than
- `-approx`	Approximately equal to
- `-bor`	Bitwise OR
- `-band`	Bitwise AND
- `-recursivematch`	Recursive match
- `-like`	Like
- `-notlike`	Not like
- `-and`	Boolean AND
- `-or`	Boolean OR
- `-not`	Boolean NOT
- Example
  ```
  Get-ADUser -Filter "name -eq 'jane doe'"
  Get-ADUser -Filter {name -eq 'jane doe'}
  Get-ADUser -Filter'name -eq "jane doe"'
  ```

## Basic LDAP Filters

- `&` and
- `|` or
- `!` not

## Example of useful queries

- `Get-ADGroup -Identity "<GROUP NAME" -Properties *` Get information about an AD group
- `whoami /priv` View a user's current rights 
- ` Get-WindowsCapability -Name RSAT* -Online \| Select-Object -Property Name, State` Check if RSAT tools are installed
- `Get-WindowsCapability -Name RSAT* -Online \| Add-WindowsCapability –Online` Install all RSAT tools
- `runas /netonly /user:htb.local\jackie.may powershell`  Run a utility as another user 
- `Get-ADObject -LDAPFilter '(objectClass=group)' \| select cn` LDAP query to return all AD groups
- `Get-ADUser -LDAPFilter '(userAccountControl:1.2.840.113556.1.4.803:=2)' \| select name` List disabled users 
- `Get-ADUser -SearchBase "OU=Employees,DC=DOMAIN-NAME,DC=LOCAL" -Filter *).count` Count all users in an OU
- `get-ciminstance win32_product \| fl` Query for installed software
- `get-ciminstance win32_product -Filter "NOT Vendor like '%Microsoft%'" | fl` Query for software that are not microsoft
- `Get-ADComputer  -Filter "DNSHostName -like 'SQL*'"` Get hostnames with the word "SQL" in their hostname
- `Get-ADGroup -Filter "adminCount -eq 1" \| select Name` Get all administrative groups
- `Get-ADUser -Filter {adminCount -eq '1' -and DoesNotRequirePreAuth -eq 'True'}` Find admin users that don't require Kerberos Pre-Auth
- `Get-ADUser -Filter {adminCount -gt 0} -Properties admincount,useraccountcontrol` Enumerate UAC values for admin users
- `Get-WmiObject -Class win32_group -Filter "Domain='DOMAIN-NAME'"` Get AD groups using WMI
- `([adsisearcher]"(&(objectClass=Computer))").FindAll()` Use ADSI to search for all computers
- `(Get-ADGroup -Identity "Help Desk" -Properties *).Member.Count` Get number of users in Help Desk Group
- `(Get-ADUser -filter * | select Name).count`Get number of Users in domain
- `(Get-ADComputer -filter * | select Name).count` Get number of Computers in domain
- `(Get-ADGroup -filter * | select Name).count` Get number of groups in domain
- `Get-ADUser -Filter {adminCount -eq '1' -and DoesNotRequirePreAuth -eq 'True'}` Filter Admin users
- `(Get-ADUser -Filter * -SearchBase "OU=IT,OU=Employees,DC=DOMAIN-NAME,DC=LOCAL").count` Find the number of users in the IT OU
- `(Get-ADUser -SearchBase "OU=Employees,DC=DOMAIN-NAME,DC=LOCAL" -Filter *).count` Count all AD Users
- `Get-ADUser -Properties * -LDAPFilter '(userAccountControl:1.2.840.113556.1.4.803:=524288)' | select Name,memberof,servicePrincipalName,TrustedForDelegation` Find user accounts marked trusted for delegation
- `Get-ADUser -Filter * -SearchBase "OU=Pentest,OU=Employees,DC=DOMAIN-NAME,DC=LOCAL"` | List user in Pentest OU
- `Get-ADGroup -filter * -Properties MemberOf | Where-Object {$_.MemberOf -ne $null} | Select-Object Name,MemberOf` Find all nested groups in the Domain
- `Get-ADDomain | Select-Object NetBIOSName, DNSRoot, InfrastructureMaster`
- `Get-ADForest | Select-Object Domains`
- `Get-ADTrust -Filter * | Select-Object Direction,Source,Target`

## Other useful native tools or cmd

- `gpresult /h gpo_report.html` Enumerate infos on Group Policy Objects (collection of policy settings) in html
- `gpresult /r /user:first.last` Get GPO for user
- `gpresult /r /S HOST` Get GPO for host

## Useful Powershell cmd

- `Set-ExecutionPolicy Unrestricted` will let you execute any ps1 script, answer A to the prompt 
- `ls -force` is the equivalent of `ls -la`

## Resources

{% embed url="https://academy.hackthebox.com/path/preview/active-directory-enumeration" %} AD on HTB Academy {% endembed %}
{% embed url="http://woshub.com/get-aduser-getting-active-directory-users-data-via-powershell/" %} Get AD User Data via Powershell {% endembed %}
{% embed url="https://vschamarti.wordpress.com/2019/11/02/powershell-commands-for-managing-active-directory/" %} Powershell commands for managing AD {% endembed %}
{% embed url="http://www.kouti.com/tables/userattributes.htm" %} User Attributes - Inside Active Directory {% endembed %}
{% embed url="http://www.kouti.com/tables/baseattributes.htm" %} Base Attributes - Inside Active Directory {% endembed %}
{% embed url="https://ldapwiki.com/wiki/" %} LDAP Wiki {% endembed %}

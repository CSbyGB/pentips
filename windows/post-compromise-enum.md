# Post-Compromise Enumeration

- We compromised a user what can we do with it
- How can we get Domain Admin

## ldapsearch - cmd line

- `ldapsearch -x -h IP-ADRESS -b "dc=DOMAIN-NAME,dc=LOCAL"` will send back a lot of useful info including user enum. Very efficient when you can leverage anonymous bind

## ldapsearch-ad.py - Example of commands

- `python3 ldapsearch-ad.py -l TARGET-IP -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t all` You will have very detailed infos on the domain and you can event output it in a file with `-o filename`
- `python3 ldapsearch-ad.py -l TARGET-IP -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t search -s '(&(objectCategory=person)(userAccountControl:1.2.840.113556.1.4.803:=262144))'` fin users with smartcard required for interactive logon 
- `python3 ldapsearch-ad.py -l TARGET-IP -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t pass-pols` You will get the password policy
- `python3 ldapsearch-ad.py -l 10.129.42.188 -u firstname.lastname -p 'password' -d DOMAIN.LOCAL -t search -s '(&(objectCategory=person)(userAccountControl:1.2.840.113556.1.4.803:=128))'` user account has their userAccountControl value set to ENCRYPTED_TEXT_PWD_ALLOWED

## Tools

{% embed url="https://github.com/yaap7/ldapsearch-ad" %} ldapsearch-ad.py - yaap7 {% endembed %}


## Resources

{% embed url="https://docs.microsoft.com/en-us/troubleshoot/windows-server/identity/useraccountcontrol-manipulate-account-properties#list-of-property-flags" %} List of AD property flags for ldap queries {% endembed %}
{% embed url="https://github.com/awsmhacks/awsmBloodhoundCustomQueries" %} Custom queries in Bloodhound - awsmhacks {% endembed %}
{% embed url="https://github.com/CompassSecurity/BloodHoundQueries/blob/master/customqueries.json" %} Custom queries in Bloodhound - Compass Security {% endembed %}
{% embed url="https://github.com/hausec/Bloodhound-Custom-Queries" %} Custom queries in Bloodhound - Hausec {% endembed %}
{% embed url="https://academy.hackthebox.com/course/preview/active-directory-bloodhound" %} ACTIVE DIRECTORY BLOODHOUND on HTB Academy {% endembed %}
{% embed url="https://sansorg.egnyte.com/dl/zscX9KYH5M" %} sans institute cheat sheet with custom db queries {% endembed %}
{% embed url="https://academy.tcm-sec.com/p/movement-pivoting-and-persistence-for-pentesters-and-ethical-hackers" %} Movement, Pivoting, and Persistence {% endembed %}
{% embed url="https://ernw.de/download/BloodHoundWorkshop/ERNW_DogWhispererHandbook.pdf" %} The dog whisperer handbook {% endembed %}

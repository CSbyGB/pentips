# Attacking Active Directory

*Note: This documentation is mostly made from my notes on [TCM Security Academy](https://academy.tcm-sec.com/)  
It will be complemented with notes from my practice and from other classes like the one from [HTB Academy](https://academy.hackthebox.com/)*

## Methodology

- Useful tool to install in kali is [pimpmykali](https://github.com/Dewalt-arch/pimpmykali) (choose 0 in option menu)
- First thing to do is launch responder (along with scans to generate traffic)
- LLMNR Poisoning
- SMB Relay Attack
- Look for websites in scope
- Check for default credentials (printers, tomcat, jenkins,...)
- Compromise a machine (as many as possible with lateral movement)
- Enumerate (network) with tools for post-compromise attack
- Get Domain Admin with post-compromise attacks
- Dump with mimikatz

## Resources

{% embed url="https://book.hacktricks.xyz/windows/active-directory-methodology" %} Hacktricks - Active Directory Methodology {% endembed %}
{% embed url="https://academy.tcm-sec.com/p/practical-ethical-hacking-the-complete-course" %} TCM-Security Academy{% endembed %}
{% embed url="https://medium.com/@adam.toscher/top-five-ways-i-got-domain-admin-on-your-internal-network-before-lunch-2018-edition-82259ab73aaa" %} Top Five Ways I Got Domain Admin - Adam Toscher {% endembed %}
{% embed url="https://adsecurity.org/" %} Active Directory Security Blog {% endembed %}
{% embed url="http://blog.harmj0y.net/" %} Harmj0y Blog {% endembed %}

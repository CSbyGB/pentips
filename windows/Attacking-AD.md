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
{% embed url="https://adam-toscher.medium.com/top-five-ways-i-got-domain-admin-on-your-internal-network-before-lunch-2018-edition-82259ab73aaa" %} Top Five Ways I Got Domain Admin on Your Internal Network before Lunch (2018 Edition) Adam Toscher {% endembed %}  
{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md" %} Active Directory Attacks - PayloadsAllTheThings {% endembed %}  
{% embed url="https://github.com/Cloud-Architekt/AzureAD-Attack-Defense" %} Azure AD - Attack and Defense Playbook - Cloud Architekt {% endembed %}  
{% embed url="https://github.com/Integration-IT/Active-Directory-Exploitation-Cheat-Sheet" %} Active Directory Exploitation Cheat Sheet - Integration IT{% endembed %}  
{% embed url="https://github.com/drak3hft7/Cheat-Sheet---Active-Directory" %} Cheat Sheet - Attack Active Directory - drak3hft7 {% endembed %}  
{% embed url="https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet" %} Active Directory Exploitation Cheat Sheet - S1ckB0y1337 {% endembed %}  
{% embed url="https://github.com/Orange-Cyberdefense/arsenal/blob/master/mindmap/pentest_ad.png" %} Pentesting active directory - Orange Cyberdéfense {% endembed %}  
{% embed url="https://1337red.wordpress.com/building-and-attacking-an-active-directory-lab-with-powershell/" %} BUILDING AND ATTACKING AN ACTIVE DIRECTORY LAB WITH POWERSHELL - 1337RED {% endembed %}  
{% embed url="https://github.com/Orange-Cyberdefense/GOAD" %} GOAD (Game Of Active Directory) - Orange Cyberdéfense {% endembed %}  
{% embed url="https://adsecurity.org/?p=2362" %} Attack Methods for Gaining Domain Admin Rights in Active Directory - Sean Metcalf - adsecurity {% endembed %}  
{% embed url="https://en.hackndo.com/" %} hackndo - Pixis {% endembed %}  
{% embed url="https://www.thehacker.recipes/ad/" %} The Hacker Recipes - Shutdown {% endembed %}  
{% embed url="https://dirkjanm.io/" %} Dirk-jan Mollema's blog {% endembed %}  

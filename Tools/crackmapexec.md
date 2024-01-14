# Crackmapexec

> Apparently the tool is no longer maintened.  
> It seems like NetExec [here](https://www.netexec.wiki/) and [here](https://github.com/Pennyw0rth/NetExec) is replacing it.  
> You can learn more about the change [here](https://github.com/byt3bl33d3r/CrackMapExec/discussions/801)

## Install

```bash
apt-get install -y libssl-dev libffi-dev python-dev-is-python3 build-essential
git clone https://github.com/Porchetta-Industries/CrackMapExec
cd CrackMapExec
poetry install
```

OR  

```bash
sudo apt-get -y install crackmapexec
```

## Launch

- `poetry run crackmapexec -h` for help

- [Enumeration with Crackmapexec](https://wiki.porchetta.industries/smb-protocol/enumeration)

## Useful commands

- `crackmapexec <protocol> <target-IP> -u <user or userlist> -p <password or passwordlist>` general usage format
- `crackmapexec smb -h` help for specific protocol (here smb)
- `crackmapexec smb 10.129.42.197 -u "user" -p "password" --shares` view the available shares and what privileges we have for them.

## Resources

- [NetExec wiki](https://www.netexec.wiki/)
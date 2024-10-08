# RPC

- Usually on port 135, 593

## Enumerate RPC

### rpcclient

- `rpcclient -U "" -c "enumdomusers" -N 10.10.10.161` enumerate users
- `rpcclient -U "" -c "enumprivs" -N 10.10.10.161 ` enumerate privileges
- Get domain info `rpcclient -U "" -c "querydominfo" -N 10.10.10.161`
- `rpcclient -U "" -c "enumdomgroups" -N 10.10.10.161` enumerate group

## Resources

{% embed url="https://www.hackingarticles.in/active-directory-enumeration-rpcclient/" %} Active Directory Enumeration: RPCClient {% endembed %}  
{% embed url="https://book.hacktricks.xyz/network-services-pentesting/135-pentesting-msrpc" %} Pentesting MSRPC {% endembed %}  
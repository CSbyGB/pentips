# HTTP and HTTPS

- Usually port 80, 443

> Checkout also the [web pentesting chapter of CSbyGB Pentips](https://csbygb.gitbook.io/pentips/web-pentesting/enumeration).

## Enumerating HTTP and HTTPS

### Default web page

- We can go check the page in the browser and see what we find if we have 30 or 443 open (or both)
- Default web page = automatic finding. Disclose info about the tecnology used: web server, version, hostname etc. = Information Disclosure

### Nikto

- We can also launch nikto `nikto -h http://host.com`
  - Example of nikto result  
![image](https://user-images.githubusercontent.com/96747355/175832200-f1f6511c-b3c7-4543-8f5b-ae7720829cdf.png)  
  - It finds possible vulnerability
  - Will even do some directory busting

### Dirbuster

- We can use dirbuster or gobuster to check if we find hidden directories (directory busting). Here is an example with dirbuster  
![image](https://user-images.githubusercontent.com/96747355/175832562-7dcac6df-58c6-4385-92b9-f2d4e11384e1.png)  
- Example of results in tree view  
![image](https://user-images.githubusercontent.com/96747355/175832618-f4e4f9e6-7f72-44c7-bb08-3e243faa5073.png)
- Example of results in list  
![image](https://user-images.githubusercontent.com/96747355/175832640-313cbe72-ce8a-49c0-9e4e-1327aa17f280.png)

### Source code

- We can select view source in the browser.
- In there we can check for comments, usernames, passwords, keys etc.

### Burpsuite

- We can use the repeater to inspect a request modify it and analyze the response

## Resources

{% embed url="https://book.hacktricks.xyz/network-services-pentesting/pentesting-web" %} Pentesting Web - Hacktricks {% endembed %}  
# Lab Setup

## Virtualbox

### Create your virtual attacking machine with Kali Linux

1. Download Virtualbox and install it: https://www.virtualbox.org/  
2. Download Virtualbox and install it from here  
3. Download the lastest kali linux virtualbox image (it is going to be our attacker machine) Make sure to take the virtualbox image and not the vmware one:

![Kali](https://user-images.githubusercontent.com/96747355/193460692-813c9c26-8baf-41ae-8d28-2a8fc89f8e38.png)

4. Install Kali:

- Go to virtualbox and click on « File » > « Import Appliance… »
- Click on the yellow folder and navigate to the image of kali you downloaded, select it and click on open
- Click on next and then click on import. It will take a little while… And then launch it for the first time. Username should be kali and password kali but you can find this info on their website or on the description of your machine in virtualbox

![Import](https://user-images.githubusercontent.com/96747355/193460766-37015043-1e20-494c-8a1e-e5890319eff7.png)

### How to use vulnerable VM to practice

- Once your have your kali installed, you can also take vulnerable machines to practice on them.
- The idea here is to connect your kali with this machine so that you can hack it from your kali.

#### Where to find vulnerable machines

- [Vulnhub](https://www.vulnhub.com/)
- [OWASP Vulnerable Web Applications Directory](https://owasp.org/www-project-vulnerable-web-applications-directory/)

**IMPORTANT NOTICE: These are vulnerable machines so use with caution. Also always check and research about a machine before installing it**

### How to connect your kali with another machine

- Once you have chosen the machine you wish to try, and deployed it, you will need to connect it.
- In this example I am going to show you how to proceed with Metasploitable 2. You can find it [here](https://www.vulnhub.com/entry/metasploitable-2,29/).

#### Install Metasploitable 2

- Unzip the downloaded file in a folder you will easily find later
- Go to virtualbox click on new machine
- Give a name to your new machine I will call it Metasploitable
- Choose the type Linux and Version Ubuntu  
![-005](https://user-images.githubusercontent.com/96747355/210256618-3477cdeb-91ed-413a-876e-b04c1a765b63.png)  
- Choose how much ram you need (1go should be enough)  
**Be careful here to also leave resources to your host and calculate this also with your kali. You will need: enough resources for your host, your kali and your vulnerable machine.**  
![-007](https://user-images.githubusercontent.com/96747355/210256688-69a8d1f9-29bf-4919-86f5-9ea5816e08ad.png)  
- On the next window click on "use an existing virtualdisk file"  
![-010](https://user-images.githubusercontent.com/96747355/210257033-bbb58dcc-6adf-4a40-8b09-2526aa939405.png)  
  - Click on the yellow folder 
  - Click on add
  - Navigate to the metasploitable folder you have just dowloaded and select the .vdmk file 
  - Select it and then click on choose
  - Finally click on create
- You can now start the machine for the first time (it should take a few minutes to start
login is `msfadmin` and password is `msfadmin`)
- Shut down the machine

### Connect Kali and Metasploitable 2 together

- Both machine should be shut down for this process
- Go to virtualbox
- Click on file > preferences > network 
- Click on the plus
- Rename the network as you like or leave it like this
- And click on ok  

![-014](https://user-images.githubusercontent.com/96747355/210257311-0fd6b87a-6539-4e66-a8f9-8a517116f686.png)  

- Click on Metasploitable
- Settings
- Network
- And select Nat Network from the dropdown menu
- And then ok
- Ensure that Allows VM is selected in promiscuous mode
- Do the Same for the kali machine
- Launch both the machine  
*For more information on connection of VM together you can refer to [this link](https://www.virtualbox.org/manual/ch06.html)*  
![-016](https://user-images.githubusercontent.com/96747355/210257515-8a19b1a2-c9f7-4dce-aa0c-127c9dea47f0.png)  

### Check if our machines can communicate

- In your Metasploitable type `ip a` and check your ip address
- In you kali open the terminal and type ping <IP-OF-METASPLOITABLE>
  - In my case: ping 10.0.2.4
  - My kali can access metasploitable  
![-018](https://user-images.githubusercontent.com/96747355/210257624-fcc053bd-131d-4532-84c8-d0e5ac4f1b85.png)  
- Now type `ip a` in your kali and ping it from your Metasploitable.
- They can connect to each other both ways.

## Docker

- `sudo apt install docker.io` install docker
- `sudo systemctl status docker` check docker status
- `sudo systemctl start docker` start docker (if not started)
- `sudo docker run hello-world` check the install
- `sudo docker pull parrotsec/security:latest` pull a parrot img
- `sudo docker run -it --name parrotos parrotsec/security`
- `sudo docker exec -it parrotos bash` start parrot OS when we need it
- If you are using virtualbox and want to use your host network you can also create a container that will do this with the hoption --net `sudo docker run -it --name parrotos-route-host parrotsec/security --net=host` 
- If you want to use files from your host (say you want to use openvpn from docker and you need to use your ovpn file) you can use the `-v` option `docker run -it --name parrotos-shared-files -v /path/on/host:/path/inside/container parrotsec/security`

### Useful commands

- `sudo docker start parrotos` start the container parrotos
- `sudo docker stop parrotos` stop a container parrotos
- `sudo docker ps` see containers
- `sudo docker stats` see the status of our containers
- `ctrl + d` exit from an existing container
- To remove an a container `sudo docker rm container-name`

## AWS

{% embed url="https://youtu.be/QWQ-LQL1owE" %} FREE Kali Linux in the Cloud (AWS)  - NetworkChuck {% endembed %}


## Resources

- [How to get started with pentesting - csbygb blog](https://raw.githubusercontent.com/CSbyGB/csbygb.github.io/main/_posts/2022-10-02-how-to-get-started.md)
- [Official guide - Metasploitatble 2 installation and details](https://docs.rapid7.com/metasploit/metasploitable-2/)
- [Official guide - Metasploitable 2 Exploitability Guide](https://docs.rapid7.com/metasploit/metasploitable-2-exploitability-guide/)
- [Building a Pentest lab with Docker - Ian Muchina](https://medium.com/@muchina/what-is-docker-2a21077b98df)
- [Running Parrot OS on Docker inside Windows! - Sepehr](https://realsepi.medium.com/running-parrot-os-on-docker-inside-windows-a8796fc11d69)
- [Docker documentation](https://docs-stage.docker.com/engine/reference/commandline/run/)
- [How to Run GUI Applications in a Docker Container - James Walker](https://www.howtogeek.com/devops/how-to-run-gui-applications-in-a-docker-container/)
# Docker

> This documentation has been made using the rooms [Intro to Containerisation](https://tryhackme.com/room/introtocontainerisation) and [Intro to Docker on TryHackMe](https://tryhackme.com/room/introtodockerk8pdqk)

## What is Containerisation

In computing terms, containerisation is the process of packaging an application and the necessary resources (such as libraries and packages) required into one package named a container. The process of packaging applications together makes applications considerably portable and hassle-free to run.  

![Diagram](../.res/2023-07-21-17-16-33.png)  

> Image Source: TryHackMe

In the screenshot above, we can see how three applications and their environments (such as dependencies) are packaged together and do not directly interact with the physical computer - but rather the containerisation engine (in this case, it is Docker)

## Intro to Docker

Docker employs the same technology used in containerisation to isolate applications into containers called the Docker Engine. The Docker Engine is essentially an API that runs on the host operating system, which communicates between the operating system and containers to access the system’s hardware (such as CPU, RAM, networking and disk)

Because of this, the Docker engine is extensive and allows you to do things like:

1. Connect containers together (for example, a container running a web application and another container running a database)
2. Export and import applications (images)
3. Transfer files between the operating system and container

Docker uses the programming syntax YAML to allow developers to instruct how a container should be built and what is run. This is a significant reason why Docker is so portable and easy to debug; share the instructions, and it will build and run the same on any device that supports the Docker Engine.  
The Docker engine allows containers to be orchestrated, meaning that multiple containers can be built as part of a group, allowing containers to communicate with each other (for example, one container running a web server and another container running a database can communicate).  

The syntax for Docker can be categorised into four main groups:

- Running a container
- Managing & Inspecting containers
- Managing Docker images
- Docker daemon stats and information

## Managing Docker Images

- `docker pull imagename` Command to download the latest version of an image example: `docker pull nginx`

| Docker Image|Tag|Command Example|Explanation|
|-------------|---|---------------|-----------|
|ubuntu|latest|`docker pull ubuntu`- IS THE SAME AS - `docker pull ubuntu:latest`|This command will pull the latest version of the "ubuntu" image. If no tag is specified, Docker will assume you want the "latest" version if no tag is specified. It is worth remembering that you do not always want the "latest". This image is quite literally the "latest" in the sense it will have the most recent changes. This could either fix or break your container.|
|ubuntu|22.04|`docker pull ubuntu:22.04`|This command will pull version "22.04 (Jammy)" of the "ubuntu" image.|
|ubuntu|20.04|`docker pull ubuntu:20.04`|This command will pull version "20.04 (Focal)" of the "ubuntu" image.|
|ubuntu|18.04|docker pull ubuntu:18.04|This command will pull version "18.04 (Bionic)" of the "ubuntu" image.|

- `docker image` list available options
- `docker image ls` list all images stored on the local machines
- `docker image rm imageId:tag` remove a docker image (we need to include the tag in the command example `ubuntu:22.04`)

## Running a container

The Docker run command creates running containers from images. This is where commands from the Dockerfile (as well as our own input at runtime) are run.

- `docker run [OPTIONS] IMAGE_NAME [COMMAND] [ARGUMENTS...]` Command synopsis to tun an image

Example:

- `docker run -it helloworld /bin/bash` We run an image named helloworld.  
"Interactively" by providing the -it switch in the [OPTIONS] command.  
This will allow us to interact with the container directly.  
We spawn a shell within the container by providing /bin/bash as the [COMMAND] part.  
This argument is where you will place what commands you want to run within the container (such as a file, application or shell!)

| [OPTION]|Explanation|Relevant Dockerfile Instruction|Example|
|---------|-----------|-------------------------------|-------|
|-d|This argument tells the container to start in "detached" mode. This means that the container will run in the background.|N/A|`docker run -d helloworld`|
|-it|This argument has two parts. The "i" means run interactively, and "t" tells Docker to run a shell within the container. We would use this option if we wish to interact with the container directly once it runs.|N/A|`docker run -it helloworld`|
|-v|This argument is short for "Volume" and tells Docker to mount a directory or file from the host operating system to a location within the container. The location these files get stored is defined in the Dockerfile|VOLUME|`docker run -v /host/os/directory:/container/directory helloworld`|
|-p|This argument tells Docker to bind a port on the host operating system to a port that is being exposed in the container. You would use this instruction if you are running an application or service (such as a web server) in the container and wish to access the application/service by navigating to the IP address.|EXPOSE|`docker run -p 80:80 webserver`|
|--rm|This argument tells Docker to remove the container once the container finishes running whatever it has been instructed to do.|N/A|docker run --rm helloworld|
|`--name`|This argument lets us give a friendly, memorable name to the container. When a container is run without this option, the name is two random words. We can use this open to name a container after the application the container is running.|N/A|`docker run --name helloworld`|

Other arguments include (but are not limited to!):

- Telling Docker what network adapter the container should use
- What capabilities the container should have access to. 
- Storing a value into an environment variable

### Listing Running Containers

- `docker ps` list running containers
- `docker ps -a` list all containers (even stopped)

#### COMING SOON

## Resources

- [Intro to Docker - TryHackMe](https://tryhackme.com/room/introtodockerk8pdqk)
- [Intro to Containerisation - TryHackMe](https://tryhackme.com/room/introtocontainerisation)
- [Docker official Documentation](https://docs.docker.com/)
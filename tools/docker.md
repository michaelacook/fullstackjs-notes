# Intro to Docker

## Why Docker?
- Docker makes it easy to make an application run on any platform and on every developer's local machine by bundling all services, libraries and dependencies together into a single container 
- Docker standardizes how an application is built and run with a simple configuration file 
- The DevOps movement integrates the tasks of software development and operations related to testing, deploying and running software
- Docker is one of the most important DevOps tools 
- DevOps prefers working systems over comprehensive documentation - Docker enables this by standardizing how systems run and allowing you to easily run an application with a simple configuration rather than having to consult a lot of documentation to get everything all set up yourself

## What is Docker?
- no need to install and configure complex supporting software or programming languages
- Dockerizing an app puts all the complexity of building and running an app into containers 
- Dockerfiles abstract away the installation of dependencies 
- Deploy to any production environment, Windows or Linux servers
- Docker containers are like a virtual machine in that they allow you to run applications and their supporting software on a host machine without touching the native OS's applications or software, but Docker containers are different in that they do not emulate a native CPU or other hardware like virtual machines do - they run directly on the host machine's hardware 
- to Dockerize an application you use a Dockerfile, which has a simple syntax
- Once you have a Dockerfile written you can use the Docker CLI to create an image from the Dockerfile 
- An imagine is a binary that contains the app defined by the Dockerfile, and can be shared with others who can run it as a container
- Docker can be used to build and deploy single apps, but it really shines when building and deploying distributed systems 
  - Docker compose - like a Dockerfile for multiple containers 
  - Docker Swarm - build, deploy and monitor multiple Docker containers at once
  - Rich networking api

## Why use Docker?
- consistency 
- quick sharing of software 
- popular, often required for jobs
- DevOps Infrastructure as Code philosophy 
- version controll for dependencies, roll back to an old version if something breaks 
- consistency between development, testing and production environments, so no surprises when you go from development to testing or production
- deploy apps to 1000s of machines

## Fundamentals of Docker 

### Virtual machines vs. containers 
- installing all dependencies for a software project locally can lead to problems, especially with sharing the project with teammates and collaborators 
- getting something that works on your machine to work on another machine can be fraught with problems 
- virtual machines attempted to solve this problem by emulating an entire computer virtually within a local computer
  - developers could install dependencies on a virtual machine, keeping them isolated from the host os and making them sharable across machines 
  - but virtual machines emulate an entire computer including hardware and firmware, leading to slow start-up
  - virtual machines are also so large they are hard to distribute, since they emulate an entire computer
- despite cons, virutal machines did help solve the problem of running software across different environments 
- 2010 Vagrant - build a virtual machine with a configuration file that specified os, software packages; but still same problems as other vms 
- 2013 Docker arrives and solves the problem with containers 
  - containers run directly on the host os but still provide an isolated environment for applications or services to run in
  - this means docker containers don't need to emulate an entire operating system. they use the host os's hardware making them way more lightware and portable
- Docker containers still provide all the same benefits of a vm without the cons. Applications and services run isolated from the host os and from each other, and are easy to transport onto any other computer or server running Docker
- [What is the difference between containers and virtual machines?](https://serverfault.com/questions/788953/what-is-the-difference-between-containers-and-virtual-machines)
- Docker cons: 
  - may not be faster than a vm outside of startup
- Docker doesn't use guest os's and instead runs on the Docker Daemon which doesn't virtualize an entire os, only the needed software packages
- unlike vms, Docker doesn't need to run an entire virtualized os for each application running on it. each container uses the daemon's resources. vms use the Hypervisor which has to create a new guest os for each application. so Docker is much more efficient

### Building blocks of Docker
- docker packages apps into images 
- when an image is run, it is a container 
- it is possible to run many instances, or containers, of an image at once 
- docker packages all the dependencies, environments, code base, plus instrutions on how to run the whole thing into a single image that can be run as a container and transported across devices with no breakage
- an image is an immutable file that is a snapshot of a container 
  - images are created with the `build` command and started up as a container with the `run` command 
- images can be stored in the docker registry 
- images can be composed of smaller images to make them more easily transported over the network
- docker uses three major components: 
  - Docker Daemon 
    - coordinates running containers, images and gets images from registries 
  - Docker registries 
    - store docker images that can be pulled to a local machine
  - Docker client
    - issues commands to Daemon 

### Docker networking
- running many containers that can communicate over a network is a strength of Docker
- it also allows you to pull in images of software such as nginx or mysql and run many servers in seprate containers on the same computer 
- Docker makes it easy for two or more containers to share a network
- To start a container for an image and expose it's port, find out the port the image exposes, start the container and publish it's port on a port your device can connect to 
- e.g 

```bash
# gets the nginx image from the Docker registry 
$ docker pull nginx 

# prints a JSON config file. To see the ports it exposes view the ExposedPorts key
$ docker inspect nginx 

# nginx exposes port 80, so we publish port 80 to our host machine port 8080, because it's available (and port 80 is not)
# the --detach option allows the process to run in the background instead of using the terminal and making it unavailable
$ docker run -p 8080:80 --detach nginx
```

- the commands above pull in and start up an nginx web server and publish it's exposed port to localhost port 8080 
- to view all running containers, use the `docker ps` command to see all docker processes

- Docker automatically makes three networks available. To view them run `docker network ls`
  - When you run a container, you can specify the network(s) it can communicate over with the `--network=[name]` command  
  - by default, Docker containers connect to the bridge network
  - to view information about a network run `docker network inspect [name]` including the containers connected to it
- stop a container by using the `docker stop [id]` command 
  - to get the id of a Docker container's process run `docker ps` to see all Docker processes. Find the container and copy it's CONTAINER ID field 

#### Connecting two containers over a network 

```bash 
# pull in a stripped down image of GNU/Linux Ubuntu
$ docker pull ubuntu

# run the Ubuntu container and create two virtual terminal sessions
$ docker run -it ubuntu --detach --name=ubuntu1
$ docker run -it ubuntu --detach --name=ubuntu2

# this command attaches the current Bash session to the container specified
# which we already gave an interactive shell
$ docker attach ubuntu1
$ apt-get update && apt-get install iputils-ping

# you can then get the IP of ubuntu2 and ping it from ubuntu1 and vice versa
```

## Building images with Dockerfiles

### Building 
-Dockerfiles are used to configure how an image is built
-the `docker build -t [options] [repository name]:[tag name] [directory]` command creates an image from the Dockerfile 
  - `e.g docker build -t sample-web-app:1.0 python`
- when you run an image as a container you have to publish the port 
  - `docker run -p [published port]:[exposed port] [name]:[tag]`
- if you're running a Docker container in the same directory as the Dockerfile, you can just use `.` for the current directory
- you can leave out the tag for the image and Docker will automatically assign a tag of "latest"
- when running a container, you can specify just the repository name without the tag and again, Docker will assign a tag of "latest"

### The `FROM` instruction 
- must be the first instruction 
- this instruction pulls in whatever image is the base for your image 
- usually an operating system that you'll run virtually in the container

### The `RUN` instruction 
- this instruction will run whatever command you want it to
- this could be bash or windows terminal shell commands (i.e. to install packages, etc)
- two forms:
  - shell form 
    - use when there are environment variables, file name wildcards whose values you want substituted into the command 
    - eg: 
    ``` 
    RUN apt-get update -y 
    RUN apt-get install -y python3
    ```
  - exec form 
    - JSON array syntax with square brackets 
    - first element in the JSON array is the executable you want to pass commands to, all other elements are arguments to that executable
    - passes arguments to the executable without invoking a shell or doing any substitution 
    - use when you want to be very precise about the arguments you're passing 
    - e.g. 
    ``` 
    RUN ["apt-get", "update", "-y"]
    ```

### `ENTRYPOINT` and `CMD` instructions
- ENTRYPOINT sets an executable to be run each time a container starts 
- if you always want the container to run the same executable, set it there 
  - both shell and exec forms are supported but exec form is preferred
- only one entrypoint per Dockerfile
- can pass additional arguments the same as you would with the RUN command
- Example of a Dockerfile to run an apache web server on a linux server: 

```
FROM debian:stable
RUN apt-get update && apt-get install -y --force-yes apache2 
EXPOSE 80 443
VOLUME ["/var/www", "/var/log/apache2", "/etc/apache2"]
ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

- note: using the FOREGROUND argument allows apache to run in the foreground instead of the background so that the process can be easily stopped with CTRL+C

- The CMD instruction receives a list of arguments that are passed to the entry point executable, if any 
  - if no entry point has been set, then the first argument given to the CMD instruction is treated as the executable 
- can only have one CMD instruction per Dockerfile 
- the value passed to the CMD instruction can be overridden by the user when they run the `docker run` command 
  - e.g:
  ```bash
  docker run -p 8080:8080 sample-web-app alternate.py
  ```
  - when `alternate.py` was passed in the run command it gets substituted for the Dockerfile's CMD argument 
- the CMD instructions can be passed in exec or shell form 

### `WORKDIR`, `COPY` and `ADD` instructions 
- WORKDIR specifies the new default directory within the image's filesystem
  - this is the location where you will probably be running your application on the image filesystem 
- any RUN, COPY or ADD instructions specified after the WORKDIR instruction will be run in the directory it specifies 
- the CMD command will run in the WORKDIR directory as well
- the COPY and ADD instructions allow you to copy files from your host machine into your image's filesystem
  - when using relative paths, your files will be copied or added relative to the WORKDIR 
  - when using absolute paths, they will be copied to the absolute path you use '
- you can view files in your WORKDIR by running `$ docker run [tag|container name] ls -R [WORKDIR]`
- ADD does everything COPY does but can also decompress files from archives or download files 
  - e.g: 
  ```docker
  # downloads the file and stores it in the WORKDIR
  ADD http://google.com/robots.txt robots.txt 

  # decompresses the tarball and saves it in the WORKDIR
  ADD zyps.tar.gz zyps
  ```
  -its best practice to only use COPY unless you really need the functionality of ADD because it can behave in unexpected ways  

### `EXPOSE` instruction 
- allows you to expose a port for other processes to make a network connection to the application or service running in the container 
- the `-p` option is used with `docker run` to publish the exposed port in the container to a port on the host machine, allowing other applications to connect to it via the port it is published to on the host machine

### `ENV` instructions 
- set environment variables that are available in the container's environment and later in the Dockerfile 
- env variables can be referenced further down in a Dockerfile with `$var` syntax  
- when you need to use an env var but can't use spaces, surround it with curly braces
- e.g 

```docker 
FROM ubuntu
ENV appDir=/app message="welcome to the app" 
WORKDIR $appDir
RUN echo $message > README.txt 
CMD echo Read ${appDir}/README.txt for a friendly greeting!
```

### `USER` instructions
- all docker commands run in the container as the root user by default
- `CMD whoami` prints the current user to the terminal
- to change the user the container withs with, specify a user after the USER command 
- if the user doesn't exist already, first use RUN useradd [username]
- the USER instruction changes the active user for entrypoint, cmd and run instructions 
- the following Dockerfile creates a non-root user, a workdir and assigns permissions for that user to only modify the workdir specified:

```docker 
FROM ubuntu
RUN useradd michael 
WORKDIR /app 
RUN chown michael /app 
USER michael 
RUN whoami > whoami.txt 
CMD whoami
```

---

## Managing images and containers 
- `docker images` to view all images on your machine
- `docker rm [container id/name]` to remove a container
- `docker rmi [image]` to remove an image, but must first remove container
- `docker stop [container name/id]` to stop a container
- `docker ps` to view all running containers 
- `docker ps -a` to view all containers running or not

### Docker registries 
- `docker login` by default login to Docker Hub 
- `docker push [name of image]`
  - to push a docker image to Docker Hub, you need to tag it with a unique tag that starts with your username 
    - `docker tag [image] [your-user-name/name]`
- after pushing an image to Docker Hub anyone else can pull it and run it as a container on their machine (if it's a public image)
  - `docker pull [username/image name]`

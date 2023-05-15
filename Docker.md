### Application Deployments
----------------------------

### Terms

* Capex: Captial Expenditure
* Opex: Operational Expenditure
* Physical Server
* Hypervisor
* Virtual Machine
* Return on Investment (ROI)

### Evolution

* Generation 1: Directly run on Physical Servers
    * Directly run on physical servers
    * If your application is not utilizing hardware completely, ROI is very long.
![Preview](./Images/docker20.png)

* Generation 2: Hypervisors create virtual machines and applications installed in virtual machines.
    * Hypervisors perform hardware virtualization and provide
        * virtual cpu
        * virtual ram
        * virtual disk
        * virtual network
    * In the isolated area created by hypervisor, we can install os and necessary softwares
    * Application can be installed and used from here
    * Better ROI.

* Generation 3: Containers: These are isolated areas which look like vms but the container is an isolated area which has virtualized os.
* Applications running in Containers will not feel the difference
* We can run more applications on a single box.
![Preview](./Images/docker21.png)

### What is Docker ?

* Docker (dock worker) is used to create containers which is standard way of packaging any application
* Application can be any of the below but have a standard way of packaging i.e docker image.
    * developed in any technology
    * developed on any server
* Packaging in docker image format helps us to run our application 

### Expectations from you in terms of Docker

* Containerize any application run by your organization.
* Manage Data, Security and Networks for containerized applications.
* To acheive the above expectations, we need to use
    * docker and understand how it runs and creates containers
    * play with docker aroud networks, data and security
    * apply them to our application.
### Run apache server (VM)
* Create a vm (ubuntu) and ssh into it
```
sudo apt update
sudo apt install apache2 -y
```
* Now navigate to ```http://<publicip>```
![Preview](./Images/docker22.png)

* Now lets try to also install nginx.

### Docker Installation

* When we install docker we get two major components
    * docker client
        * A command line to interact with docker engine.
    * docker engine: This is collection of multiple components
        * Orchestration
        * Docker daemon
        * Runtime
![Preview](./Images/docker23.png)
* To play the docker commands
    * manual --help
    * docker cheatsheet.
* Install
    * Linux VM:
        * Docker can be installed by following instructions over here [Refer Here](https://docs.docker.com/engine/install/)
        * Script based installtion [Refer Here](https://get.docker.com/)
        ``` 
        curl -fsSL https://get.docker.com -o get-docker.sh
        sh get-docker.sh
        ```
        ![Preview](./Images/docker24.png)
        * Docker allows communication to the unix socket for the users who belong to docker group. so lets add current user to docker group ```sudo usermod -aG docker <username>```. logout and login
        * Now execute ```docker container run hello-world```
        ![Preview](./Images/docker25.png)

### Docker Playground
* Create a Docker Hub account [Refer Here](https://hub.docker.com/)
* This playground gives a linux machine with docker installed for 4 hours for free. [Refer Here](https://labs.play-with-docker.com/)

#### Terms to be aware of
* containerd
* runc
* libcontainer
* oci
* docker shim
* appc
* grpc
* rkt containers

### Cheatsheets
* [Refer Here](https://docs.docker.com/get-started/docker_cheatsheet.pdf)
* [Refer Here](https://dockerlabs.collabnix.com/docker/cheatsheet/)


* Applications bring revenue to enterprises and to run applications we need servers, os etc

### Lets create a linux server and install tomcat in it

* Create a linux server (AWS/Azure/GCP)
![Preview](./Images/docker26.png)
* We have experimented in the linux vm
    * network interface gives network connectivity
    * cpu, ram and disk are available
    * to install softwares we have used package manager apt
* Commands
```
sudo apt update
sudo apt install net-tools openjdk-11-jdk tomcat9 -y
# to check network
ifconfig
# to check java
java -version
# to check tomcat
sudo systemctl status tomcat9
```
* We were able to exactly the similar operations inside container as well.

### Let me take an application

* This is spring pet clinic application
* Lets try to run this application on linux
[Refer Here](https://github.com/spring-projects/spring-petclinic)
* To build the code
```
sudo apt update
sudo apt install openjdk-17-jdk maven -y
git clone https://github.com/spring-projects/spring-petclinic.git
cd spring-petclinic
# java package
mvn package
java -jar target/spring-petclinic-3.0.0-SNAPSHOT.jar
```
* Docker way of working
    * We create a docker image (docker packaging format)
        * We need to create Dockerfile
        * push the image to registry (docker hub)
        * create the container using the image anywhere
* Dockerfile
```
FROM amazoncorretto:17-alpine-jdk
LABEL author=krishna
ADD target/spring-petclinic-3.0.0-SNAPSHOT.jar /springpetclinic.jar
EXPOSE 8080
CMD ["java", "-jar", "/springpetclinic.jar"]
```
* Create docker image
``` docker image build -t spc:1.0 .```
* To create a container 
```
docker conrainer run -d -P spc:1.0
docker conrainer run -d -P spc:1.0
docker conrainer run -d -P spc:1.0
```

### How Isolations are created or How Containers Work
* Each container is getting a
    * new process tree
    * disk mounts
    * network (nic)
    * cpu/memory
    * users
* Navigate to docker internals section notes and try to understand the things.
![Preview](./Images/docker27.png)

### Docker Architecture

#### Generation-1
* This was first gen, Where docker daemon used lxc (a linux kernel feature) to create containers
![Preview](./Images/docker28.png)

#### Generation-2
* Since docker was relying on lxc which was kernel feature, updates to kernel frequently used to break containers created by docker.
* So docker has created its own component called libcontainer (libc) to create containers.
* Docker wanted containers to be multi os and lxc was definetly not the way forward.
![Preview](./Images/docker29.png)
* Adoption of docker was drastically increased as it was stable.
### Generation 3:
* In this generation, docker engine was revamped from monolith to multi component architecture and the images and containers were according to OCI (open container initiative) image spec and runtime spec.
* In the latest architecture
* docker daemon exposes api’s to listen requests from docker client.
* Passes the requests to containerd. This manages the lifecylcle of container
* containerd forks a runc process which creates container. once the container is created the parent of the container will be docker shim.
![Preview](./Images/docker30.png)

### Creating our first docker container
* docker container creation:
* To create container we need some image in this case lets take ```hello-world```
* The command ```docker container run hello-world``` executed
* What happens
    * docker client will forward the request to docker daemon
    * docker daemon will check if the image exists locally. if yes creates the container by using image
    * if the image doesnot exist, then docker daemon tries to download the image from docker registry connected. The default docker registry is docker hub.
    * Downloading image into local repo from registy is called as pull.
    * Once the image is pulled the container is created.
    ![Preview](./Images/docker31.png)
* Registry is collection of docker images hosted for reuse.
* Docker hub [Refer Here](https://hub.docker.com/search?q=)

#### Playing with containers 
* Create a new linux vm and install docker in it
![Preview](./Images/docker32.png)
#### Check docker images in the host
![Preview](./Images/docker33.png)
![Preview](./Images/docker34.png)
![Preview](./Images/docker35.png)

### pull the images from docker hub
* image naming convention
```
[username]/[repository]:[<tag>]
madasu/myspc:1.0.1
username => madasu
repository => what image => myspc
tag => version => 1.0.1
```
* default tag is latest
```
nginx
nginx:latest
```
* official images dont have username
```
nginx
ubuntu
alpine
madasu/myspc
```
* Lets pull the image nginx with tag 1.23
```
docker image pull nginx:1.23
docker image ls
```
![Preview](./Images/docker36.png)
* Lets pull the jenkins image with latest version
![Preview](./Images/docker37.png)
* Lets find the alpine and pull the image
![Preview](./Images/docker38.png)

#### Remove images from local
* Every image will have unique image id and image name
* We can delete individually docker image rm alpine:3.17
* if i have to delete all the images ```docker image rm $(docker image ls -q)```
![Preview](./Images/docker39.png)
![Preview](./Images/docker40.png)

#### Create a container with nginx
* To create and start the container we use run command
![Preview](./Images/docker41.png)
* every container gets an id and a name. name can be passed while creating container, if not docker will give random name
![Preview](./Images/docker42.png)
* Remove all the running containers ```docker container rm -f $(docker container ls -q )```
![Preview](./Images/docker43.png)
![Preview](./Images/docker44.png)
* Remove specific container
![Preview](./Images/docker45.png)
* Remove all containers ```docker container rm -f $(docker container ls -a -q )```
![Preview](./Images/docker46.png)

### Docker container lifecycle
* Docker lifecycle states
    * Created
    * Running
    * Paused
    * Stopped
    * Deleted
![Preview](./Images/docker47.png)
* Accessing the applications inside docker containers
* From now the machine where we have installed docker will referred as host and the docker container will be referred as container
* We have access to host network & as of now containers are created in private container network, so to access applications inside containers we use port-forwording
![Preview](./Images/docker48.png)
* command: ```docker container run -d -p <host-port>:<container-port> <image>```
* Create a nginx container and expose on port 30000 ```docker container run -d -p 30000:80 --name nginx1 nginx```
![Preview](./Images/docker49.png)
* Create a jenkins container & expose 8080 port on 30001 port of host ```docker container run -d -p 30001:8080 --name jenkins1 jenkins/jenkins```
![Preview](./Images/docker50.png)
* To assing any random free port on host to container port ```docker container run -d -P image```
* Lets create 3 nginx containers
![Preview](./Images/docker51.png)
![Preview](./Images/docker52.png)

#### Containerizing spring petclinic
* I have spring petclinic version 2.4.2 which requires java 11 and runs on port 8080
* to start application java -jar spring-petclinic-2.4.2.jar
* What is required:
    * jdk 11
    * jar file
* How to access application
* http over port 8080
* Lets start the amazoncorretoo based container with port 8080 exposed [Refer Here](https://hub.docker.com/_/amazoncorretto)
```
docker container run -it -p 30000:8080 amazoncorretto:11 /bin/bash
```
![Preview](./Images/docker53.png)
* now lets download the spring petclinic [Refer Here](https://referenceapplicationskhaja.s3.us-west-2.amazonaws.com/spring-petclinic-2.4.2.jar)
```
curl https://referenceapplicationskhaja.s3.us-west-2.amazonaws.com/spring-petclinic-2.4.2.jar -o spring-petclinic-2.4.2.jar
ls
```
* Run the application ```java -jar spring-petclinic-2.4.2.jar```
![Preview](./Images/docker54.png)
*  Now to create a image from a running container, lets login into linux vm, so lets use ```docker container commit```
![Preview](./Images/docker55.png)
* remove all the containers and run the myspc image based container
```
docker container run -d -p 30001:8080 --name spc1 myspc:latest java -jar spring-petclinic-2.4.2.jar
```
* This is not a useful approach as we are creating images manually
* Docker has a better way i.e. ```Dockerfile```

### Dockerfile based Image building
* Workflow
![Preview](./Images/docker56.png)
* Dockerfile is a text file with instructions [Refer Here](https://docs.docker.com/engine/reference/builder/)
* The basic syntax ```INSTRUCTION arguments```
* In Docker we have concept of base image i.e. to run your application using some existing image
* We can use a base image called as scratch which has nothing in it
* In majority of the cases we take what is required to run our application as base image.

### Basic Instructions
* FROM: [Refer Here](https://docs.docker.com/engine/reference/builder/#from) for official docs. use tag all the time (donot use latest)
* RUN: The commands to be executed while building the image to install/configure your appliation [Refer Here](https://docs.docker.com/engine/reference/builder/#run)
* CMD: This command will be executed while starting the container. [Refer Here](https://docs.docker.com/engine/reference/builder/#cmd) for official docs
* EXPOSE: This adds ports to be exposed while starting the container [Refer Here](https://docs.docker.com/engine/reference/builder/#expose) for official docs


### Springpetclinic Dockerfile
* Lets do two ways
    * use any image with java11 already as base image ```amazoncorretto:11```
    * use any image with slim os as base image  ```alpine:3```
* Dockerfile based on amazoncorreto:11
```Dockerfile
FROM amazoncorretto:11
RUN curl https://referenceapplicationskhaja.s3.us-west-2.amazonaws.com/spring-petclinic-2.4.2.jar -o spring-petclinic-2.4.2.jar
EXPOSE 8080
CMD ["java", "-jar", "spring-petclinic-2.4.2.jar"]
```
* Lets build the image based on amazoncorreto
![Preview](./Images/docker57.png)
![Preview](./Images/docker58.png)
* Now lets create a container ```docker container run -d -P --name spc1 myspc:corretto11```
![Preview](./Images/docker59.png)

* Approach 2: Start from some os
```Dockerfile
FROM alpine:3
RUN apk add openjdk11
RUN wget https://referenceapplicationskhaja.s3.us-west-2.amazonaws.com/spring-petclinic-2.4.2.jar
EXPOSE 8080
CMD ["java", "-jar", "spring-petclinic-2.4.2.jar"]
```
* Build the image
![Preview](./Images/docker60.png)
![Preview](./Images/docker61.png)
* Lets run the container ```docker container run -d -P --name myspc2 myspc:alpine```
![Preview](./Images/docker62.png)

### Immutable Infrastructure
* Any infra changes will not be done on infra directly rather we create some infra as code option and change the configuration.
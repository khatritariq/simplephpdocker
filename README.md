# simplephpdocker
Simple PHP Docker Setup
# Docker Files

Apache 2.4.37 with PHP 7.2 image

MySQL 5.7

## Developer

Tariq Mehmood 
# Installation Instructions

## Install Docker

 1. Download latest  version of docker  from the following link [https://download.docker.com/linux/ubuntu/dists/xenial/pool/stable/amd64/](https://download.docker.com/linux/ubuntu/dists/xenial/pool/stable/amd64/)
 2. Run this command, change the downloaded file path.
 
	 `sudo dpkg -i /path/to/package.deb`

3. Verify that Docker CE is installed correctly by running the `hello-world` image.

	`sudo docker run hello-world`

## Install Docker Compose
1. Run this command to download the latest version of Docker Compose:

	`sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
    
    **Note : Use the latest Compose release number in the download command.**
    
2. Apply executable permissions to the binary:

	`sudo chmod +x /usr/local/bin/docker-compose`

## Build the Containers
1. Turn off the local services of Apache Server, Redis and MySQL.
2. Go to the root of the repo from terminal, where docker-compose.yml is situated and run the command:

	`sudo docker-compose build`
    
    **NOTE: Building the container will take sometime depending on your internet speed. There will be some warnings in the process. Ignore them as they will not interrupt the installation. Also if you make any changes in the docker file, you have to build again**
    
3. After success, run the following command to make containers up.

	`sudo docker-compose up -d`
	
4. Likewise if you want to make the containers down, run the command:

	`sudo docker-compose down`
    
    **Note: When you make any changes in docker-compose.yml, you have to down the container first. Also changes in .conf file will not take effect until you down and the up the container.**


By now your docker containers are up and running. To see the list of running containers, run the following command:

    sudo docker ps --all

Also you can SSH to containers using the following command.

    sudo docker exec -it ''ContainerID'' bash 

## Points To Remember
1. **Applications Folder**

    applications folder on the root of the repo is where all the project will be cloned. This folder is added in the .gitignore. No changes will be pushed in the folder. 

2. **Ports Configuration**

    Ports no 8071, 8072 and 8073 are reserved for php 7.2 application. You may choose any available port.
     
3. **Vhosts**

    Vhost.conf folder is present in centos-php-apache-7 folder.
    
4. **Server Configuration**

    If you have to make any changes related to server configuration, don't do it directly on container because those changes will be vanished when you remove and build the container again. Instead, do it on respected docker file and create pull request so that other can get benefit from it.
    
5. **MySQL Configuration**

    To connect the mysql database using sqlyog use:

    host > localhost

    username > root

    password > mysql123
    
    port > 3307 
    
    But, if you want to connect from PHP application, use 'db' instead of 'localhost'.

That's all for now, have fun Dockerizing! 

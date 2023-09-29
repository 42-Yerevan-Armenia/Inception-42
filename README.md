# Inception-42

## Table of Contents
1. [Introduction](#introduction)
    - [What is a Virtual Machine?](#virtual-machine)
2. [Installation](#installation)
    - [Step 1: Installing & Configuring SSH](#step-1-installing--configuring-ssh)
    - [Step 2: Installing & Configuring UFW](#step-2-installing--configuring-ufw)
    - [Step 3: Connecting to Server with SSH](#step-3-connecting-to-server-with-ssh)
    - [Step 4: Configuring *user*](#step-4-configuring-user)
    - [Step 5: Configuring *sudo*](#step-5-configuring-sudo)
    - [Step 6: Make directories](#step-6-make-directories)
3. [Install Certificate](#install-certificat)
    - [Step 1: Mkcert](#step-1-mkcert)
    - [Step 2: Hostname](#step-2-hostname)
    - [Step 3: Geting certificates](#step-3-geting-certificates)
    - [Step 4: Test project](#step-3-test-project)
        - [Test https](#test-https)
4. [Makefile](#makefile)
4. [Nginx](#nginx)
    - [Step 1: What is Docker?](#what-is-docker)
    - [Step 2: Creation of Dokerfile](#creation-of-dockerfile)
    - [Step 3: Config file](#config-file)
    - [Step 4: Docker configuration](#docker-configuration)



## Introduction
You will create your first machine in VirtualBox (or UTM if you can’t use VirtualBox) under specific instructions. Then, at the end of this project, you will be able to set up your own operating system while implementing strict rules.

### <a name="virtual-machine">What is a Virtual Machine?</a>

  <a>
    <img src="https://upload.wikimedia.org/wikipedia/commons/d/d5/Virtualbox_logo.png?20150209215936" width="100" height="100">
  </a>

Virtualization allow us share a system with multiple virtual environments. The hypervisor manages the hardware system and separate the physical resources from the virtual environments. **The resources are managed followitn the needs, from the host to the guests.** When an user from a VM do a task that requires additional resources from the physical environment, the hypervisor manages the request so that the guest OS could access the resources of the physical environment.<br>
Once we know how they work, it is a good idea to see all the advantages we get from using virtual machines:
<ul>
 <li>Different guest machines hosted on our computer <b>can run different operating systems</b>, so we will have different OS working on the same machine.</li>
   <li>They provide an environment in which <b>to safely test unstable programs</b> to see if they will affect the system or not.</li>
   <li>We get <b>better use of shared resources.</b></li>
   <li>We <b>reduce costs</b> by reducing physical architecture.</li>
   <li>They are <b>easy to implement</b> because they provide mechanisms to clone a virtual machine to another physical device.</li>
</ul>

## Installation
At the time of writing, the latest stable version of [debian](https://www.debian.org/) is *Debian 12 bookworm*, but i have done on [debian](https://download.g0tmi1k.com/iso/Debian/Debian-11/debian-11.6.0-amd64-netinst.iso) 11, [here](https://www.youtube.com/watch?v=poCSq_0OmjE) is the link of 11 instalation guide. I use system with

  - RAM 4GB
  - CPU 4
  - Memory 50+GB
  - username with your intra_login
  - Sowtware only SSH

### Step 1: Configuring SSH

`apt update`

Install *vim*, `apt install vim`

`vim /etc/ssh/sshd_config`

Set up SSH using Port 42

>13 Port 42

Enable SSH login as *root*

>32 PermitRootLogin yes

Turning off key connection

>17PubkeyAuthentication no

Enable connection with password

>36PasswordAuthentication yes

Check SSH status.
`service ssh status`

Start and stop the SSH Server
`service ssh start/stop`


### Step 2: Installing & Configuring UFW
Install *ufw*.

`apt install ufw`

Enable Firewall.
`ufw enable`

Configure the rules. Allow incoming connections using Port 42, 80 and 443.
`ufw allow 42`

Check UFW status.
`ufw status`

Add forward rule for VirtualBox.

1. Go to VirtualBox-> Choose the VM->Select Settings 
2. Choose “Network”-> “Adapter 1"->”Advanced”->”Port Forwarding”
<a>
    <img src="https://miro.medium.com/max/1342/1*rCj_FeuZ5Rm2abz48qhulg.png">
</a>
3. Enter the values as shown:
<a>
    <img src="https://github.com/codesshaman/inception/blob/main/media/ports_forwarding/step_6.png?raw=true">
</a>

Restart your VM.
`reboot`

### Step 3: Connecting to Server with SSH

SSH into your VM using Port 42. Type the line below into Terminal in your physicall machin.
```
ssh <username>@<ip-address> -p 42
```

For first let's install other softs that we need

```
apt install -y sudo docker docker-compose make openbox xinit kitty firefox-esr
```

### Step 4: Configuring *user* 

Add user to *sudo* and *docker* group.

`usermod -aG sudo docker <username>`

Verify whether user was successfully added to *sudo* and *docker* group.

`getent group sudo`

### Step 5: Configuring *sudo*

⚠️You risk to loose your project if you type something else in this file and saved it⚠️

`vim /etc/sudoers`

🛡️If you type something wrong, it will show you the error and ask for saving like that🛡️

`sudo visudo /etc/sudoers`

###
Add user to root privilege

`<username> ALL=(ALL:ALL) ALL`

`reboot` for changes to take effect.

Switch tu user, from now we will work with sudo user.

`su -`

`cd ~/`

### Step 6: Make directories

`make_directories.sh`

```
#!/bin/bash
mkdir project
mkdir project/srcs
touch project/Makefile
mkdir project/srcs/requirements
touch project/srcs/docker-compose.yml
touch project/srcs/.env
echo "DOMAIN_NAME=<username>.42.fr" > project/srcs/.env
echo "CERT_=./requirements/tools/<username>.42.fr.crt" >> project/srcs/.env
echo "KEY_=./requirements/tools/<username>.42.fr.key" >> project/srcs/.env
echo "DB_NAME=wordpress" >> project/srcs/.env
echo "DB_ROOT=rootpass" >> project/srcs/.env
echo "DB_USER=wpuser" >> project/srcs/.env
echo "DB_PASS=wppass" >> project/srcs/.env
echo "MYSQL_ROOT_PASSWORD=123456" >> project/srcs/.env
echo "MYSQL_USER=dbuser" >> project/srcs/.env
echo "MYSQL_PASSWORD=1235" >> project/srcs/.env
mkdir project/srcs/requirements/bonus
mkdir project/srcs/requirements/mariadb
mkdir project/srcs/requirements/mariadb/conf
touch project/srcs/requirements/mariadb/conf/create_db.sh
mkdir project/srcs/requirements/mariadb/tools
touch project/srcs/requirements/mariadb/tools/.gitkeep
echo "" > project/srcs/requirements/mariadb/tools/.gitkeep
touch project/srcs/requirements/mariadb/Dockerfile
touch project/srcs/requirements/mariadb/.dockerignore
echo ".git" > project/srcs/requirements/mariadb/.dockerignore
echo ".env" >> project/srcs/requirements/mariadb/.dockerignore
mkdir project/srcs/requirements/nginx
mkdir project/srcs/requirements/nginx/conf
touch project/srcs/requirements/nginx/conf/nginx.conf
mkdir project/srcs/requirements/nginx/tools
touch project/srcs/requirements/nginx/Dockerfile
echo ".git" > project/srcs/requirements/mariadb/.dockerignore
echo ".env" >> project/srcs/requirements/mariadb/.dockerignore
mkdir project/srcs/requirements/tools
mkdir project/srcs/requirements/wordpress
mkdir project/srcs/requirements/wordpress/conf
touch project/srcs/requirements/wordpress/conf/wp-config-create.sh
mkdir project/srcs/requirements/wordpress/tools
touch project/srcs/requirements/wordpress/tools/.gitkeep
echo "" > project/srcs/requirements/wordpress/tools/.gitkeep
touch project/srcs/requirements/wordpress/Dockerfile
touch project/srcs/requirements/wordpress/.dockerignore
echo ".git" > project/srcs/requirements/wordpress/.dockerignore
echo ".env" >> project/srcs/requirements/wordpress/.dockerignore
```

Run it `sh make_directories.sh`

## Install Certificate

### Step 1: Mkcert

Let's first do update

`sudo apt update -y`

Install tools that will help us

`sudo apt install -y wget curl libnss3-tools`

Download mkcert

`curl -s https://api.github.com/repos/FiloSottile/mkcert/releases/latest| grep browser_download_url  | grep linux-amd64 | cut -d '"' -f 4 | wget -qi -`

Rename it

`mv mkcert-v*-linux-amd64 mkcert`

Give a permission

`chmod a+x mkcert`

And finally move to working directory (in other OS it maybe /usr/bin/)

`sudo mv mkcert /usr/local/bin/`

Let's check it *v1.4.4*

`mkcert --version`

### Step 2: Hostname

Let's add a new one

`sudo vim /etc/hosts`

Add `<username.42.fr>` next to localhost

### Step 3: Geting certificates

First let's go to

`cd ~/project/srcs/requirements/tools/`

For get it let's run our *mkcert*

`mkcert <username>.42.fr`

Now let's rename files for *nginx*

`mv <username>.42.fr-key.pem <username>.42.fr.key`

`mv <username>.42.fr.pem <username>.42.fr.crt`

### Step 4: Test project

Return to home

`cd ~`

Clone this test docker

`git clone https://github.com/codesshaman/simple_docker_nginx_html.git`


Let's run it `cd simple_docker_nginx_html`

`docker-compose up -d`

`sudo startx`

Select Web browser and go to *localhost* or *username.42.fr* 

<a>
    <img src="https://github.com/codesshaman/inception/blob/main/media/setting_docker/step_4.png?raw=true">
</a>

You can turn it off with *Ctrl + C*

Now let's change HTTP to HTTPS

`~/simple_docker_nginx_html/nginx/conf.d/nginx.conf`

Clear it and add this

```
server {

    listen      80;
    listen      443 ssl;
    server_name  <username.42.fr www.<username.42.fr;
    root    /var/www/public/html;
    #if ($scheme = 'http') {
    #    return 301 https://<username.42.fr$request_uri;
    #}
    ssl_certificate     /etc/nginx/ssl/<username.42.fr.crt;
    ssl_certificate_key /etc/nginx/ssl/<username.42.fr.key;
    ssl_protocols            TLSv1.2 TLSv1.3;
    ssl_session_timeout 10m;
    keepalive_timeout 70;
    location / {
        try_files $uri /index.html;
    }
}
```

Now let's back and stop container

`cd ~/simple_docker_nginx_html/ && docker-compose down`

Then open our docker and add in volumes the key

`docker-compose.yml`

`- /home/${USER}/project/srcs/requirements/tools:/etc/nginx/ssl`

Here how it will look like

```
version: '3'

services:
  nginx:
    image: nginx:stable-alpine
    volumes:
      - ./public:/var/www/public/
      - ./nginx/conf.d:/etc/nginx/conf.d/
      - /home/${USER}/project/srcs/requirements/tools:/etc/nginx/ssl/
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    container_name: simple_nginx_html
```

#### Test https

Now let's run our test-docker and go to web by startx

`docker-compose up -d`

If you see this, don't worry because our own certificat don't have security cualification.

<a>
    <img src="https://github.com/codesshaman/inception/blob/main/media/install_certificate/step_6.png?raw=true">
</a>

Max we can do here just *Advanced ...* + *Accept the Risk and Contine*

Sometimes it will didn't work, don't worry after some manipulation and magic it will work.

`docker-compose down`

## Makefile

Now let's create our makefile

`cd ~/project/ && vim Makefile`

```
name = inception
all:
	@printf "Launch configuration ${name}...\n"
	@bash srcs/requirements/wordpress/tools/make_dir.sh
	@docker-compose -f ./srcs/docker-compose.yml --env-file srcs/.env up -d

build:
	@printf "Building configuration ${name}...\n"
	@bash srcs/requirements/wordpress/tools/make_dir.sh
	@docker-compose -f ./srcs/docker-compose.yml --env-file srcs/.env up -d --build

down:
	@printf "Stopping configuration ${name}...\n"
	@docker-compose -f ./srcs/docker-compose.yml --env-file srcs/.env down

re: clean all

clean: down
	@printf "Cleaning configuration ${name}...\n"
	@docker system prune -a
	@sudo rm -rf ~/data/mariadb/*
	@sudo rm -rf ~/data/wordpress/*

fclean:
	@printf "Total clean of all configurations docker\n"
	@docker stop $$(docker ps -qa)
	@docker system prune --all --force --volumes
	@docker network prune --force
	@docker volume prune --force
	@sudo rm -rf ~/data/wordpress/*
	@sudo rm -rf ~/data/mariadb/*

.PHONY	: all build down re clean fclean
```

Start of our docker we do by `docker-compose up -d` command for *docker-compose.yml* config file. Here we use -f flag which takes the path of congif file.

Construction of container we do by `docker-compose up -d --build` command.

And we stop our docker by `docker-compose down` command.

`docker system prune --a` Remove all unused images not just dangling ones.

And finly for total fclean first we stop out container by `docker stop $$(docker ps -qa)` and delete by *--force* flag everything, also network, volume and dates.

## Nginx

<a>
    <img src="https://github.com/codesshaman/inception/blob/main/media/nginx_deploy/step_1.png?raw=true">
</a>

Okey now let's take what it need

| Soft | For what |  Port |
| :----------: | :---------------------------: | :----: |
| Ngninx |Proxying web server | 443 |
| PHP | Scripting language for the web | - |
| PHP-Fpm | A set of libraries for FastCGI API | 9000 |
| Wordpress | Content Management System | - |
| MariaDB | Relational database | 3306 |

### Step 1: What is Docker?

A Docker image is a set of environments necessary to run certain software. It differs from virtualbox-type emulators in that the container does not contain a full-fledged operating system, the container uses the Linux kernel and not everything is placed inside it, but only the programs and libraries necessary to run the software. Thus, the container weighs significantly less than the emulated system x20.

### Step 2: Creation of Dockerfile

In Docker, a special file called Dockerfile is responsible for the configuration. It specifies a set of software that we want to deploy inside this container. Let's go to `cd ~/project/srcs/requirements/nginx/`

`vim Dockerfile`

```
FROM alpine:3.16
RUN	apk update && apk upgrade && apk add --no-cache nginx
EXPOSE 443
CMD ["nginx", "-g", "daemon off;"]
```

We write in it the *FROM* instruction, which shows from which image we will deploy our container. As we remember from *subject* we can't use labels like *alpine:latest*, we must go to the official sites and take the latest or earlier version like `alpine:3.16` or `debian:buster`.

Next, we specify what software and how we want to install it inside the container. It is not possible to run an application directly from RUN. In some cases this can be done through a script, but in general the CMD and ENTRYPOINT instructions are used to run it. RUN creates a static layer, changes inside which are written to the image, but do not cause anything, CMD and ENTRYPOINT run something, but DO NOT write changes to the image. Therefore, you should not execute scripts with them, the result of which must be “put” into the final image or partition. There is RUN for this. We can say that changes made through RUN are static. For example, installing packages on a system like ours.

Then we need to open the port 443 on which the container will exchange traffic.

In the end we have to run the installed configuration. To do this, use the CMD instruction.

This way we run nginx directly and not in daemon mode. Daemon mode is a launch mode in which the application starts in the background or, in Windows parlance, as a service. For ease of debugging, we disable this mode and receive all nginx logs directly into the tty of the container.

### Step 3: Config file

Let's add config file `vim conf/nginx.conf`

Since we have already trained with a test container, let’s take a similar configuration, changing it for php so that it allows reading not html, but php wordpress files. We will no longer need port 80, since according to the guide we can only use port 443. But at the first stage, we will comment out the sections responsible for php and temporarily add html support (for testing):

```
server {
    listen      443 ssl;
    server_name  <intra_login>.42.fr www.<intra_login>.42.fr;
    root    /var/www/;
    index index.php index.html;
    ssl_certificate     /etc/nginx/ssl/<intra_login>.42.fr.crt;
    ssl_certificate_key /etc/nginx/ssl/<intra_login>.42.fr.key;
    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_session_timeout 10m;
    keepalive_timeout 70;
    location / {
        try_files $uri /index.php?$args /index.html;
        add_header Last-Modified $date_gmt;
        add_header Cache-Control 'no-store, no-cache';
        if_modified_since off;
        expires off;
        etag off;
    }
#    location ~ \.php$ {
#        fastcgi_split_path_info ^(.+\.php)(/.+)$;
#        fastcgi_pass wordpress:9000;
#        fastcgi_index index.php;
#        include fastcgi_params;
#        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
#        fastcgi_param PATH_INFO $fastcgi_path_info;
#    }
}
```

Port 9000 is exactly the port of our php-fpm, through which the connection between php and nginx is made. And wordpress in this case is the name of our container with wordpress.

Also let's copy our keys here `cp ~/project/srcs/requirements/tools/* ~/project/srcs/requirements/nginx/tools/`

### Step 4: Docker configuration

Docker-compose is a system for launching Docker containers; one might say, it is a kind of add-on to Docker. If in docker files we specified what software to install inside one container environment, then with docker-compose we can control the launch of many similar containers at once, launching them with one command.

Let's modify our file `cd ../../ && vim docker-compose.yml`

```
version: '3'

services:
  nginx:
    build:
      context: .
      dockerfile: requirements/nginx/Dockerfile
    container_name: nginx
#    depends_on:
#      - wordpress
    ports:
      - "443:443"
    volumes:
      - ./requirements/nginx/conf/:/etc/nginx/http.d/
      - ./requirements/nginx/tools:/etc/nginx/ssl/
      - /home/${USER}/simple_docker_nginx_html/public/html:/var/www/
    restart: always
```

Now we have done here (you can delete this repo)
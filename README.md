# Inception-42

## Table of Contents
1. [Installation](#installation)
    - [Step 1: Configuring SSH](#step-1-configuring-ssh)
    - [Step 2: Installing & Configuring UFW](#step-2-installing--configuring-ufw)
    - [Step 3: Connecting to Server with SSH](#step-3-connecting-to-server-with-ssh)
    - [Step 4: Configuring *user*](#step-4-configuring-user)
    - [Step 5: Configuring *sudo*](#step-5-configuring-sudo)
    - [Step 6: Make directories](#step-6-make-directories)
2. [Install Certificate](#install-certificate)
    - [Step 1: Mkcert](#step-1-mkcert)
    - [Step 2: Hostname](#step-2-hostname)
    - [Step 3: Geting certificates](#step-3-geting-certificates)
    - [Step 4: Test project](#step-4-test-project)
        - [Test https](#test-https)
3. [Makefile](#makefile)
4. [Nginx](#nginx)
    - [Step 1: What is Docker?](#step-1-what-is-docker)
    - [Step 2: Creation of Dokerfile](#step-2-creation-of-dockerfile)
    - [Step 3: Nginx config file](#step-3-nginx-config-file)
    - [Step 4: Docker config for Nginx](#step-4-docker-config-for-nginx)
5. [MariaDB](#mariadb)
    - [Step 1: Dockerfile](#step-1-dockerfile)
    - [Step 2: MariaDB config file](#step-2-mariadb-config-file)
    - [Step 3: Call script in Dockerfile](#step-3-call-script-in-dockerfile)
    - [Step 4: Docker config for MariaDB](#step-4-docker-config-for-mariadb)
    - [Step 5: Check MariaDB](#step-5-check-mariadb)
6. [WordPress](#wordpress)
    - [Step 1: Again Dockerfile](#step-1-again-dockerfile)
    - [Step 2: Docker config for WordPress](#step-2-docker-config-for-wordpress)
    - [Step 3: Volume & Network](#step-3-volume--network)
    - [Step 4: Data](#step-4-data)
    - [Step 5: WordPress config file](#step-5-wordpress-config-file)
        - [Auto user](#auto-user)
7. [Finish](#finish)


## Introduction
You will create your first machine in VirtualBox (or UTM if you can’t use VirtualBox) under specific instructions. Then, at the end of this project, you will be able to set up your own operating system while implementing strict rules.

### <a name="virtual-machine">What is a Virtual Machine?</a>

  <a>
    <img src="https://upload.wikimedia.org/wikipedia/commons/d/d5/Virtualbox_logo.png?20150209215936" width="100" height="100">
  </a>

**The resources are managed followitn the needs, from the host to the guests.** When an user from a VM do a task that requires additional resources from the physical environment, the hypervisor manages the request so that the guest OS could access the resources of the physical environment.<br>
Once we know how they work, it is a good idea to see all the advantages we get from using virtual machines:
<ul>
 <li>Different guest machines hosted on our computer <b>can run different operating systems</b>, so we will have different OS working on the same machine.</li>
   <li>They provide an environment in which <b>to safely test unstable programs</b> to see if they will affect the system or not.</li>
   <li>We get <b>better use of shared resources.</b></li>
   <li>We <b>reduce costs</b> by reducing physical architecture.</li>
   <li>They are <b>easy to implement</b> because they provide mechanisms to clone a virtual machine to another physical device.</li>
</ul>

## Installation
At the time of writing, the latest stable version of [debian](https://www.debian.org/) is *Debian 12 bookworm*, but i have done on [debian](https://download.g0tmi1k.com/iso/Debian/Debian-11/debian-11.6.0-amd64-netinst.iso) 11, here is the [link](https://www.youtube.com/watch?v=poCSq_0OmjE) of instalation guide. I use system with

  - RAM 4GB
  - CPU 4
  - Memory 50+GB
  - username with your username
  - Sowtware only SSH

<a><img src="https://lucloi.vn/wp-content/uploads/2019/12/maxresdefault-58.jpg"></a>

### Step 1: Configuring SSH

`apt update`

Install *vim*, `apt install vim`

`vim /etc/ssh/sshd_config`

Set up SSH using Port 42

>15 Port 42

Enable SSH login as *root*

>34 PermitRootLogin yes

Turning off key connection

>39 PubkeyAuthentication no

Enable connection with password

>58 PasswordAuthentication yes

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
<a><img src="https://miro.medium.com/max/1342/1*rCj_FeuZ5Rm2abz48qhulg.png"></a>
3. Enter the values as shown:
<a><img src="https://github.com/codesshaman/inception/blob/main/media/ports_forwarding/step_6.png?raw=true"></a>

Restart your VM.
`reboot`

### Step 3: Connecting to Server with SSH

SSH into your VM using Port 42. Type the line below into Terminal in your physicall machin.
```
ssh <username>>@<ip-address> -p 42
```

If you see this message don't scare, it just says that the Port is already registered and we need just delete it and try again
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

Delete it from here
```
vim /Users/<username>>/.ssh/known_hosts
```

For first let's install other softs that we need

```
apt install -y sudo docker docker-compose make openbox xinit kitty firefox-esr
```

<a><img src="https://media.makeameme.org/created/and-then-we-9544377ac5.jpg" weith=200px, height=200px></a>

### Step 4: Configuring *user* 

Add user to *sudo* and *docker* group.

`usermod -aG sudo <username>` `usermod -aG docker <username>`

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

Switch to user, from now we will work with sudo user.

`su <username>`

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
echo "DOMAIN_NAME=<username>>.42.fr" > project/srcs/.env
echo "CERT_=./requirements/tools/<username>>.42.fr.crt" >> project/srcs/.env
echo "KEY_=./requirements/tools/<username>>.42.fr.key" >> project/srcs/.env
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

<a><img src="https://www.irishnews.com/picturesarchive/irishnews/irishnews/2017/09/02/203021043-b1845cb1-4309-450d-b26e-fb45979758b7.jpg" weith=400px, height=400px></a>

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

Add `<username>.42.fr>` next to localhost

### Step 3: Geting certificates

First let's go to

`cd ~/project/srcs/requirements/tools/`

For get it let's run our *mkcert*

`mkcert <username>>.42.fr`

Now let's rename files for *nginx*

`mv <username>>.42.fr-key.pem <username>>.42.fr.key`

`mv <username>>.42.fr.pem <username>>.42.fr.crt`

<a><img stc="https://media.musclegrid.io/glensfallskarate.com/uploads/2020/01/21040811/jackie-chan-wait-what-meme.jpg"></a>

### Step 4: Test project

Return to home

`cd ~`

Clone this test docker

`git clone https://github.com/codesshaman/simple_docker_nginx_html.git`


Let's run it `cd simple_docker_nginx_html`

`docker-compose up -d`

`sudo startx`

Select Web browser and go to *localhost* or *username.42.fr* 

<a><img src="https://github.com/codesshaman/inception/blob/main/media/setting_docker/step_4.png?raw=true"></a>

You can turn it off with *Ctrl + C*

Now let's change HTTP to HTTPS

`vim ~/simple_docker_nginx_html/nginx/conf.d/nginx.conf`

Clear it and add this

```
server {

    listen      80;
    listen      443 ssl;
    server_name  <username>.42.fr www.<username>.42.fr;
    root    /var/www/public/html;
    #if ($scheme = 'http') {
    #    return 301 https://<username>.42.fr$request_uri;
    #}
    ssl_certificate     /etc/nginx/ssl/<username>.42.fr.crt;
    ssl_certificate_key /etc/nginx/ssl/<username>.42.fr.key;
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

`vim docker-compose.yml`

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

<a><img src="https://github.com/codesshaman/inception/blob/main/media/install_certificate/step_6.png?raw=true"></a>

Max we can do here just *Advanced ...* + *Accept the Risk and Contine*

Sometimes it will didn't work, don't worry after some manipulation and magic it will work. Now we have done here (you can delete this repo).

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

<a><img src="https://github.com/codesshaman/inception/blob/main/media/nginx_deploy/step_1.png?raw=true"></a>

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

### Step 3: Nginx config file

Let's add config file `vim conf/nginx.conf`

Since we have already trained with a test container, let’s take a similar configuration, changing it for php so that it allows reading not html, but php wordpress files. We will no longer need port 80, since according to the guide we can only use port 443. But at the first stage, we will comment out the sections responsible for php and temporarily add html support (for testing):

```
server {
    listen      443 ssl;
    server_name  <username>>.42.fr www.<username>>.42.fr;
    root    /var/www/;
    index index.php index.html;
    ssl_certificate     /etc/nginx/ssl/<username>>.42.fr.crt;
    ssl_certificate_key /etc/nginx/ssl/<username>>.42.fr.key;
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

<a><img src="https://img.universitystudent.org/1/1/2/i-understand-nothing-meme.jpg"></a>

### Step 4: Docker config for Nginx

Docker-compose is a system for launching Docker containers; one might say, it is a kind of add-on to Docker. If in docker files we specified what software to install inside one container environment, then with docker-compose we can control the launch of many similar containers at once, launching them with one command.

Let's modify our file `cd ../../ && vim docker-compose.yml` Be carefull, typing Tabs one more or less Tab will blow your .yml 

```
version: '3'

services:
  nginx:
    build:
      context: .
      dockerfile: requirements/nginx/Dockerfile
    container_name: nginx
    depends_on:
      - wordpress
    ports:
      - "443:443"
    networks:
      - inception
    volumes:
      - ./requirements/nginx/conf/:/etc/nginx/http.d/
      - ./requirements/nginx/tools:/etc/nginx/ssl/
      - wp-volume:/var/www/
    env_file:
      - .env
    restart: always
```

First in *services:* is our *nginx*, then the path of Dockerfile, also giving a name *nginx* and port. Remember that *nginx* builts faster than wordpress, thats why we need to wait for WordPress container to be built and start only after. 

We add sections so that the container sees our config and our keys, and we also make sure to mount our /var/www - that will run nginx. Later we will take files from the WordPress directory.

<a><img src="https://media.makeameme.org/created/time-to-relax-5001808360.jpg" weith=200px, height=200px></a>

## MariaDB

### Step 1: Dockerfile

`cd ~/project/srcs`

`vim requirements/mariadb/Dockerfile`

```
FROM alpine:3.16

RUN apk update && apk add --no-cache mariadb mariadb-client

RUN mkdir /var/run/mysqld; \
    chmod 777 /var/run/mysqld; \
    { echo '[mysqld]'; \
      echo 'skip-host-cache'; \
      echo 'skip-name-resolve'; \
      echo 'bind-address=0.0.0.0'; \
    } | tee  /etc/my.cnf.d/docker.cnf; \
    sed -i "s|skip-networking|skip-networking=0|g" \
      /etc/my.cnf.d/mariadb-server.cnf

RUN mysql_install_db --user=mysql --datadir=/var/lib/mysql

EXPOSE 3306

USER mysql
COPY tools/db.sh .
ENTRYPOINT  ["sh", "db.sh"]
CMD ["/usr/bin/mysqld", "--skip-log-error"]
```

Here we run the RUN instruction in which we install the mariadb and mariadb-client we need without caching. Next, in the same RUN we normalize our working configuration. We do this with one RUN because each RUN directive creates a new layer in the docker image, and it is better not to create extra RUNs unnecessarily. The tee command sends the output of echo to a file, and the sed command replaces lines in files by value. This way we set the minimum required set of settings without creating unnecessary configs inside one docker file.

With the second layer we create a database from what we installed and configured on the previous layer. We indicate the path where the default database will be stored. Then we open the mariadb working port and switch to the mysql user created when installing the database.

And finally, we launch the database under this user.

### Step 2: MariaDB config file

`vim requirements/mariadb/conf/create_db.sh`

```
#!bin/sh

if [ ! -d "/var/lib/mysql/mysql" ]; then

        chown -R mysql:mysql /var/lib/mysql

        # init database
        mysql_install_db --basedir=/usr --datadir=/var/lib/mysql --user=mysql --rpm

        tfile=`mktemp`
        if [ ! -f "$tfile" ]; then
                return 1
        fi
fi

if [ ! -d "/var/lib/mysql/wordpress" ]; then

        cat << EOF > /tmp/create_db.sql
USE mysql;
FLUSH PRIVILEGES;
DELETE FROM     mysql.user WHERE User='';
DROP DATABASE test;
DELETE FROM mysql.db WHERE Db='test';
DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1');
ALTER USER 'root'@'localhost' IDENTIFIED BY 'rootpass';
CREATE DATABASE wordpress CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE USER 'wpuser'@'%' IDENTIFIED by 'wppass';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'%';
FLUSH PRIVILEGES;
EOF
        # run init.sql
        /usr/bin/mysqld --user=mysql --bootstrap < /tmp/create_db.sql
        rm -f /tmp/create_db.sql
fi
```

The first block of code checks if mysql is running and if not, starts it. Checking in case something bad happens; in fact, our mysql must be installed and running, and this block is usually skipped.

The second block checks whether a database named wordpress exists. Of course, we don’t have it, and, falling inside this block, we write the sql code to create this database into the file for sql queries. The code uses environment variables that we will pass from the env file. In the same block, we execute this code and delete the extra configuration file, skillfully covering our tracks.

### Step 3: Call script in Dockerfile

`vim requirements/mariadb/Dockerfile`

```
FROM alpine:3.16

ARG DB_NAME \
    DB_USER \
    DB_PASS

RUN apk update && apk add --no-cache mariadb mariadb-client

RUN mkdir /var/run/mysqld; \
    chmod 777 /var/run/mysqld; \
    { echo '[mysqld]'; \
      echo 'skip-host-cache'; \
      echo 'skip-name-resolve'; \
      echo 'bind-address=0.0.0.0'; \
    } | tee  /etc/my.cnf.d/docker.cnf; \
    sed -i "s|skip-networking|skip-networking=0|g" \
      /etc/my.cnf.d/mariadb-server.cnf

RUN mysql_install_db --user=mysql --datadir=/var/lib/mysql

EXPOSE 3306

COPY requirements/mariadb/conf/create_db.sh .
RUN sh create_db.sh && rm create_db.sh
USER mysql
CMD ["/usr/bin/mysqld", "--skip-log-error"]
```

As you see we call it in script

```
COPY requirements/mariadb/conf/create_db.sh .
RUN sh create_db.sh && rm create_db.sh
```

But for the script to work, we must pass environment variables to create the database. Environment variables are taken from the .env file; these are the same secrets that, as a rule, are stored separately from the repository code.

```
ARG DB_NAME \
    DB_USER \
    DB_PASS
```

Docker has two ways to pass environment variables to an image: via ARG and via ENV. Through ARG, those arguments are passed that will be used when building the image, and will not be used after it starts. Therefore, we will transfer all our secrets through the ARG, so that they do not hang around the already assembled image.

Variables that will be in the environment of an already running container are passed through ENV. We will not use them for our tasks.

<a><img src="https://i.imgflip.com/2kujgf.jpg" weith=200px, height=200px></a>

### Step 4: Docker config for MariaDB

`vim docker-compose.yml`

```
  mariadb:
    build:
      context: .
      dockerfile: requirements/mariadb/Dockerfile
      args:
        DB_NAME: ${DB_NAME}
        DB_USER: ${DB_USER}
        DB_PASS: ${DB_PASS}
        DB_ROOT: ${DB_ROOT}
    container_name: mariadb
    ports:
      - "3306:3306"
    networks:
      - inception
    volumes:
      - db-volume:/var/lib/mysql
    env_file:
      - .env
    restart: always
```

As we can see, our variables are passed to the ARG through the args section in the build section. They can only be passed here, because they are launched only during the build and are not present in the image, unlike ENVs, which are passed through the environment section already inside the service.

Mariadb runs on port 3306, so this port must be open.

### Step 5: Check MariaDB

In order to check if everything worked out, we need to run the following command after starting the container:

`docker exec -it mariadb mysql -u root`

This will put us in the text environment of the database *MariaDB [(none)]>*

Here we run `show databases;` command and you will see this

```
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| wordpress          |
+--------------------+
5 rows in set (0.001 sec)
```

At the bottom there must be a database we created with the name wordpress! If it is not there, then our script did not work correctly or did not work at all. This may be due to many reasons - the script file was not copied, the environment variables were not transferred, the wrong values were written in the .env file...

But if everything is done correctly, then congratulations - we have successfully launched the database!

We exit the environment using the exit command or Ctrl+D.

## WordPress

### Step 1: Again Dockerfile

<a><img src="https://s.yimg.com/ny/api/res/1.2/NiuCf2JkvlZAz0aTNXbI7g--/YXBwaWQ9aGlnaGxhbmRlcjtoPTY2Ng--/https://media.zenfs.com/en-us/news.mashable/a6f793f75c83be84ab10eab5aeca37b0" weith=200px, height=200px></a>

`vim requirements/wordpress/Dockerfile`

```
FROM alpine:3.16
ARG PHP_VERSION=8 \
    DB_NAME \
    DB_USER \
    DB_PASS
RUN apk update && apk upgrade && apk add --no-cache \
    php${PHP_VERSION} \
    php${PHP_VERSION}-fpm \
    php${PHP_VERSION}-mysqli \
    php${PHP_VERSION}-json \
    php${PHP_VERSION}-curl \
    php${PHP_VERSION}-dom \
    php${PHP_VERSION}-exif \
    php${PHP_VERSION}-fileinfo \
    php${PHP_VERSION}-mbstring \
    php${PHP_VERSION}-openssl \
    php${PHP_VERSION}-xml \
    php${PHP_VERSION}-zip \
    php${PHP_VERSION}-redis \
    wget \
    unzip && \
    sed -i "s|listen = 127.0.0.1:9000|listen = 9000|g" \
      /etc/php8/php-fpm.d/www.conf && \
    sed -i "s|;listen.owner = nobody|listen.owner = nobody|g" \
      /etc/php8/php-fpm.d/www.conf && \
    sed -i "s|;listen.group = nobody|listen.group = nobody|g" \
      /etc/php8/php-fpm.d/www.conf && \
    rm -f /var/cache/apk/*
WORKDIR /var/www
RUN wget https://wordpress.org/latest.zip && \
    unzip latest.zip && \
    cp -rf wordpress/* . && \
    rm -rf wordpress latest.zip
COPY ./requirements/wordpress/conf/wp-config-create.sh .
RUN sh wp-config-create.sh && rm wp-config-create.sh && \
    chmod -R 0777 wp-content/
CMD ["/usr/sbin/php-fpm8", "-F"]
```

I will indicate the PHP version in a variable - a command line argument. Sets the variable instruction ARG. Also, using this instruction, I accept three arguments from our .env file with secrets - the database name, user name and password.

The difference is that an ARG with parameters sets an environment variable with the passed parameter, while an ARG without parameters takes a parameter from the same variable in docker-compose.

First, let's list the basic components: this is php, on which our wordpress runs, php-fpm for interacting with nginx and php-mysqli for interacting with mariadb.

For the full operation of our WordPress, we will not skimp and load all the required modules, omitting the caching and additional modules. For the bonus part, we will also install the redis module. We’ll also download the wget package needed to download Wordpress itself, and the unzip package for unzipping the archive with downloaded Wordpress.

CMD launches our installed php-fpm (attention: the version must match the installed one!).

### Step 2: Docker config for WordPress

`vim docker-compose.yml`

```
  wordpress:
    build:
      context: .
      dockerfile: requirements/wordpress/Dockerfile
      args:
        DB_NAME: ${DB_NAME}
        DB_USER: ${DB_USER}
        DB_PASS: ${DB_PASS}
    container_name: wordpress
    depends_on:
      - mariadb
    networks:
      - inception
    volumes:
      - wp-volume:/var/www/
    env_file:
      - .env
    restart: always
```

The depends_on directive means that wordpress depends on mariadb and will not start until the container with the database is built. The fastest of our containers will be nginx - due to its light weight, it will be assembled and launched first. But the database and CMS take approximately the same amount of time to assemble, and to prevent wordpress from starting to install on a database that has not yet been deployed, you will need to specify this dependency. Next, we will transfer the same “secrets” stored in the .env file to the container.

### Step 3: Volume & Network

Nginx and wordpress should have a common section for data exchange. The assignment also requires a partition to store the database. And all this should be stored in our /home//data. You can mount the same folder here and there, but for convenience, let’s create a partition by specifying the path to its folder:

```
volumes:
  wp-volume:
    driver_opts:
      o: bind
      type: none
      device: /home/${USER}/data/wordpress

  db-volume:
    driver_opts:
      o: bind
      type: none
      device: /home/${USER}/data/mariadb
```

Next, according to the task, we must combine our containers into a single network. In fact, all containers that are registered within one docker-compose - the file or configuration of which are located in the same folder - are automatically combined into a common network. However, the name of the network is not set by us. But turning to the network is sometimes useful.

In order for our network to be accessible to us by name, let's create, in addition to the default one, our own network. It is created extremely simply:

```
networks:
    inception:
        driver: bridge
```

The full file you can see in this repo.

### Step 4: Data

`vim requirements/wordpress/tools/make_dir.sh`

```
#!/bin/bash
if [ ! -d "/home/${USER}/data" ]; then
        mkdir ~/data
        mkdir ~/data/mariadb
        mkdir ~/data/wordpress
fi
```

This code checks for the presence of the data folder in the user's folder, and if not, creates all the necessary folder configurations.

Let's give him permission and if you want you can run it and check `ls ~/data/`

`chmod +x requirements/wordpress/tools/make_dir.sh`

### Step 5: WordPress config file

`vim requirements/wordpress/conf/wp-config-create.sh`

```
#!bin/sh
if [ ! -f "/var/www/wp-config.php" ]; then
cat << EOF > /var/www/wp-config.php
<?php
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wpuser' );
define( 'DB_PASSWORD', 'wppass' );
define( 'DB_HOST', 'mariadb' );
define( 'DB_CHARSET', 'utf8' );
define( 'DB_COLLATE', '' );
define('FS_METHOD','direct');
\$table_prefix = 'wp_';
define( 'WP_DEBUG', false );
if ( ! defined( 'ABSPATH' ) ) {
define( 'ABSPATH', __DIR__ . '/' );}
define( 'WP_REDIS_HOST', 'redis' );
define( 'WP_REDIS_PORT', 6379 );
define( 'WP_REDIS_TIMEOUT', 1 );
define( 'WP_REDIS_READ_TIMEOUT', 1 );
define( 'WP_REDIS_DATABASE', 0 );
require_once ABSPATH . 'wp-settings.php';
EOF
fi
```

Let's pay attention to $table_prefix = 'wp_'; To prevent an empty string from being written to $table_prefix (since we don’t have such a variable in bash), we must escape the string with a backslash - “\”.

Some settings regarding redis will be useful to us only in the bonus part. Basically, they won’t bother us either.

#### Auto user

Now we need to create a script that will write our wordpress dates from .env, and when we run our localhost it will show us our domain page and not WordPress-admin register

```
wp core install --url=localhost --title="my insception" --admin_user=${WP_ADMIN_USR} --admin_password=${WP_ADMIN_PWD} --admin_email=${WP_ADMIN_EMAIL}
if wp user get Johnik >/dev/null 2>&1; then
    echo "User Johnik exists."
else
   wp user create ${WP_USR} ${WP_EMAIL} --role=subscriber --user_pass=${WP_PWD}
fi
wp user update "username" --user_pass="${WP_PWD}" --skip-email
if wp user get "username" >/dev/null 2>&1; then
    echo "Logged in successfully as root."
else
    echo "Login failed. Please check your root credentials."
fi
wp theme activate twentytwentytwo
/usr/sbin/php-fpm8 -F
```

## Finish

Now lets run our Makefil for first time and check if all works? If yes run those commands and check the outputs:

`docker exec -it wordpress ps aux | grep 'php'`

```
1 root      0:00 {php-fpm8} php-fpm: master process (/etc/php8/php-fpm.conf
9 nobody    0:00 {php-fpm8} php-fpm: pool www
10 nobody    0:00 {php-fpm8} php-fpm: pool www
```

`docker exec -it wordpress php -v`

```
PHP 8.0.22 (cli) (built: Aug  5 2022 23:54:32) ( NTS )
Copyright (c) The PHP Group
Zend Engine v4.0.22, Copyright (c) Zend Technologies
```

`docker exec -it wordpress php -m`

```
[PHP Modules]
Core
curl
date
dom
exif
fileinfo
filter
hash
json
libxml
mbstring
mysqli
mysqlnd
openssl
pcre
readline
Reflection
SPL
standard
xml
zip
zlib
[Zend Modules]
```

Perfect we have finished our project

<a><img src="https://media.makeameme.org/created/voila.jpg" weith=200px, height=200px></a>

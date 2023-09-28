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
At the time of writing, the latest stable version of [Debian](https://www.debian.org/) is *Debian 12 bookworm*, but i have done on 11, [here](https://www.youtube.com/watch?v=poCSq_0OmjE) is the link of 11 instalation guide. I use system with
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

Sometimes it will didn't work, don't worry after some manipulation and magic it will work, now we have done here (you can delete this repo)

`docker-compose down`


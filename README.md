# Docker Image Builder/Deployer of Joomla4 v1

## What does this script do
This script can build Joomla docker image, and can deploy it as well with docker-compose file that gets autogenerated based on your configurations.

You need [Docker](https://docs.docker.com/engine/install/) and [Docker Compose](https://docs.docker.com/compose/install/) installed, and then make sure the user that runs this script has permission to run docker. Also make sure you do not have another service listening at port 80, so stop Apache. We may still change the default ports to also be configurable, but for now we are using port 80, 81, 82. 

## To run
__Before__ you run this script setup your __config.properties__ using the __config.tmpl__ as your example.
```bash
$ clone https://github.com/vdm-io/Joomla-Docker.git
$ cd Joomla-Docker
$ sudo chmod +x run.sh
$ ./run.sh -f config.properties
```
# Docker

```docker
docker push vdmio/joomla:4.0.0-beta5
```

## The following Environment Variables are Available:

```text
### Website User Details
    WEBSITEDOMAIN=builder.vdm
    WEBSITESNAME="Joomla - VDM"
    WEBSITESUNAME="Joomla Method"
    WEBSITESUSERNAME=method
    WEBSITESUSERPASS=vastdevelopmentmethod
    WEBSITESPASSRESET=0
    WEBSITESEMAIL="joomla@example.com"
    WEBSITESADDPATCHTESTER=1

### Website database details
    DBDRIVER=mysqli
    DBHOST=mysql
    DBUSER=vdm
    DBPASS=vastdevelopmentmethod
    DBROOTPASS=vastdevelopmentmethod
    DBNAME=joomla
    DBPREFIX=vdm

### Website email details
    SMTPHOST=mailcatcher

### Ports
    PORTWEB=80
    PORTSSL=443
    PORTPAM=81
    PORTMC=82

### Volumes
    VOLWEBROOT=web-root
    VOLDBHOST=db-data

### Packages
    # PACKAGEJOOMA="https://developer.joomla.org/nightlies/Joomla_4.0.0-beta5-dev-Development-Full_Package.zip" # just in build
    PACKAGEPATCHTESTER="https://github.com/joomla-extensions/patchtester/releases/download/4.0.0/com_patchtester.zip"
```

# Docker Compose

```yml
version: '3.8'

services:
  web:
    # image created for this task
    image: vdmio/joomla:4.0.0-beta5
    container_name: joomla
    restart: unless-stopped
    environment:
      WEBSITEDOMAIN: builder.vdm
      WEBSITESNAME: "Joomla - VDM"
      WEBSITESUNAME: "Joomla Method"
      WEBSITESUSERNAME: method
      WEBSITESUSERPASS: vastdevelopmentmethod
      WEBSITESPASSRESET: 0
      WEBSITESEMAIL: "joomla@example.com"
      WEBSITESADDPATCHTESTER: 1
      DBDRIVER: mysqli
      DBHOST: mysql
      DBUSER: vdm
      DBPASS: vastdevelopmentmethod
      DBROOTPASS: vastdevelopmentmethod
      DBNAME: joomla
      DBPREFIX: vdm
      SMTPHOST: mailcatcher
      STARTUP_COMMAND_1: deploy_joomla
    links:
      - mysql
      - mailcatcher
    depends_on:
      - mysql
    volumes:
       - web-root:/var/www/html
    ports:
      - 80:80
      - 443:443

  mysql:
    image: mysql:5.7
    container_name: mysql
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: vastdevelopmentmethod
    volumes:
       - db-data:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: phpmyadmin
    restart: unless-stopped
    environment:
      PMA_HOST: mysql
      PMA_PORT: 3306
      # PMA_USER: root
      # PMA_PASSWORD: vastdevelopmentmethod
    links:
      - mysql
    ports:
      - 81:80

  mailcatcher:
    image: schickling/mailcatcher
    restart: unless-stopped
    container_name: mailcatcher
    ports:
      - 82:1080

volumes:
    web-root:
    db-data:
```

# Shout-out to:
- [Thecodingmachine PHP docker project](https://github.com/thecodingmachine/docker-images-php)
- [Digital-Peak](https://github.com/Digital-Peak/DPDocker)
- [Joomla Console Project](https://github.com/joomlatools/joomlatools-console)


```txt
Llewellyn van der Merwe <llewellyn.van-der-merwe@community.joomla.org>
Copyright (C) 2019. All Rights Reserved
GNU/GPL Version 2 or later - http://www.gnu.org/licenses/gpl-2.0.html
```

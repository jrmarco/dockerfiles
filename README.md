# Docker-files
This repository contains a list of ready-to-use dockerfile image to build docker containers. Check Dockerfiles list for specifications
## List: 
* webserver-php74: Ubuntu 20-04 container
Comes with Apache2, MariaDb, PHP 7.4 ( minimal libraries ), cUrl, wget, nodejs, npm, nano, git and composer.
* webserver-phalcon: Ubuntu 20-04 container
Comes with Apache2, MariaDb, PHP 7.4, cUrl, wget, nodejs, npm, nano, git, composer, Phalcon framework and Phalcon DevTools
* webdev-php8-2204: Ubuntu 22-04 container
Comes with Apache2, MariaDb, PHP 8.1, xdebug, cUrl, wget, nodejs, npm, nano, git, composer

Last update 3rd December 2022
**PLEASE NOTE: all this images are meant to be used only as dev-environment**

## Other files in this repo:
* 000-default.conf: default apache2 virtualhost directory settings.  
Default configuration bind http://localhost:80 with directory /var/www/html inside the container. Edit this file as you prefer, but keep it in the same dockerfile directory when building image, since is used during building process
```
<VirtualHost *:80>
    ServerName localhost
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        <Directory /var/www/html>
                Options FollowSymLinks
                AllowOverride All
                Require all granted
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
* phalcon.ini: apache2 module to enable Phalcon library.   
Used only with ***webserver-phalcon*** image build. Keep this file in the same dockerfile directory when building image, since is used during building process
```
; configuration for php opcache module
; priority=50
extension=phalcon.so
```

## Build an image, run a container :
* Docker must be already installed on your system and running
* To build a new image type the following command:  
`docker build . -f *<dockerFilename>* -t *<Name>:<Tag>`
    * . : will pick required files from current directory
    * -f <dockerFilename>: path to dockerfile to build image from
    * -t <Name>:<Tag>: name and tag to be assigned to image, optional
* When the image is build you can run a container from this image with the following command:  
`docker run -d -v <pathToLocalDir>/<pathToContainerDir> -p 80:80 -p 3306:3306 <imageId> OR <name>:<tag>`
    * -d : run container detached from current terminal you are working with
    * -v : bind local directory with a directory inside the container ( use absolute path )
    * -p : instruct docker to bind ports between local machine and container
        * 80:80 : bind local machine port 80 with the container port 80 ( used by apache2 )
        * 3306:3306 : bind local machine port 3306 with the container port 3306 ( used by mariadb )
    * imageId OR name:tag: specify the image used to build container, via imageID or name:tag of the image
* To log via terminal into your container, type:  
`docker exec -t -i <containerId> OR <containerName> bash`
    * -t : open a pseudo terminal TTY
    * -i : keep STDIN open
    * containerId OR containerName : specify the container to log into
    * bash: specify the type of terminal
You are now running a container and you are able to log into it! Good job

## Setup Mariadb and enable connection from the local host machine:
When container is up and running, Mariadb is partially configured with default options. In case you want to set it properly I suggest you to start with `mysql_secure_installation` where you can tweak main configurations, set root password and allow login from remote. In case you need to reach mariadb from the host machine with a Sql client, do this steps:
* log into the container
* edit file `/etc/mysql/mariadb.conf.d/50-server.cnf` ( you can use vi or nano )
* change `bind-address = 127.0.0.1` into `bind-address = 0.0.0.0`
* run / restart mariadb
* log into mysql
* create a new root account with local container ip:  
`CREATE USER 'root'@'<localIp>' IDENTIFIED BY '<rootPassword>';`
* give all privileges:  
`GRANT ALL PRIVILEGES ON *.* TO 'root'@'<localIp>' WITH GRANT OPTION;`
* refresh privileges:  
`flush privileges;`
* Local-Ip : usually docker set as default subnet address starting from 172.17.0.? . You can check your from the docker settings or doing an inspect within the active container

# Disclaimer
Provided dockerimages and derived container from this images are provided with the "AS IT IS" formula and are meant to be used only in a dev-environment and not in a production environment. Creator is not responsible for any damage/abuse arising the use of this files or docker container. Creator is not resposible and cannot be legally responsible for any breaking law that involves the use of this dockerfile or containers.

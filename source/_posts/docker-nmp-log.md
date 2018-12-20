---
title: Docker-Nginx-Mysql-PHP笔记
date: 2017-06-01
tags:
 - Docker
---

```bash
docker pull mysql:latest
docker run --name mysql -e MYSQL_ROOT_PASSWORD=kaychen -d docker.io/mysql
mkdir -p /opt/mysql/
mkdir -p /opt/mysql/conf
docker cp mysql:/var/lib/mysql/ /opt/mysql/
docker cp mysql:/etc/mysql/conf.d/mysql.cnf /opt/mysql/conf/
mv /opt/mysql/mysql /opt/mysql/data
docker rm -f mysql

docker run --name mysql \
-p 127.0.0.1:3306:3306 \
-v /opt/mysql/conf:/etc/mysql/conf.d/ \
-v /opt/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=kaychen \
-d docker.io/mysql
```
```bash
docker pull nginx
docker run -d --name nginx docker.io/nginx
mkdir /opt/nginx/wwwroot/
docker cp nginx:/etc/nginx/nginx.conf /opt/nginx
docker cp nginx:/etc/nginx/conf.d /opt/nginx
docker cp nginx:/usr/share/nginx/html /opt/nginx/wwwroot/
docker rm -f nginx
docker run --name nginx \
-p 80:80 \
-p 443:443 \
-v /opt/nginx/nginx.conf:/etc/nginx/nginx.conf \
-v /opt/nginx/conf.d:/etc/nginx/conf.d \
-v /opt/nginx/wwwroot:/home/wwwroot \
-v /var/log/nginx:/var/log/nginx \
--link php7:php7 \
-d docker.io/nginx
```

```bash
docker run -d --name php53 -v /home/wwwroot:/home/wwwroot -p 127.0.0.1:9000:9000 kaychen/php53 php-fpm
docker run -d --name php7 -v /home/wwwroot:/home/wwwroot docker.io/php:7.0-fpm
docker run -d --name nginx --link php7:php7 --link php53:php53 -v /home/wwwroot:/home/wwwroot -v /root/nginx/vhost:/etc/nginx/conf.d -p 80:80 docker.io/nginx
```
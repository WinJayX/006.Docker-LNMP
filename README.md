# Docker-LNMP



### docker-compose.yml

```yaml
version: '3'
services:

  ### Nginx container ########Version:1.16.1#################################

  nginx:
      hostname: nginx
      build:
        context: MountData/components/nginx
        dockerfile: Dockerfile
      ports:
        - "${HTTP_PORT1}:80"
      volumes:
        - ${PROJECT_FOLDER}:/nginxapp
        - ./MountData/components/nginx/log:/var/log/nginx
      restart: always
      privileged: true
      networks:
        net-lnmp:
          ipv4_address: 10.127.1.2

  ### PHP container ###########Version:7.4.1##############################

  php:
      hostname: php
      build:
        context: MountData/components/php
        dockerfile: Dockerfile
        args:
          TIME_ZONE: ${GLOBAL_TIME_ZONE}
          CHANGE_SOURCE: ${GLOBAL_CHANGE_SOURCE}
      volumes:
        - ${PROJECT_FOLDER}:/phpapp
        - ./MountData/components/php/log:/var/log
      restart: always
      privileged: true
      networks:
        net-lnmp:
          ipv4_address: 10.127.1.3


  ### Mysql container #########################################

  mariadb:
      hostname: mariadb
      image: harbor.nercoa.com/winjay/mariadb:10.4.11
      ports:
        - "${MYSQL_PORT}:3306"
      volumes:
        - ./MountData/components/mariadb/data:/var/lib/mysql
        - ./MountData/components/mariadb/config/mysql.cnf:/etc/mysql/conf.d/mysql.cnf
        - ./MountData/components/mariadb/log:/var/log/mariadb
      restart: always
      privileged: true
      environment:
        MYSQL_ROOT_PASSWORD: ${MYSQL_PASSWORD}
      networks:
        net-lnmp:
          ipv4_address: 10.127.1.4

networks:
  net-lnmp:
    ipam:
      config:
        - subnet: 10.127.1.0/24
```







### Docker-File-PHP

```dockerfile
FROM centos:WinJay
MAINTAINER www.winjay.cn@WinJayX
RUN yum install epel-release -y && \
    yum clean all && \
    rm -rf /var/cache/yum/*
RUN yum install -y gcc gcc-c++ make gd-devel libxml2-devel libcurl-devel libjpeg-devel libpng-devel openssl-devel sqlite-devel
RUN wget http://docs.php.net/distributions/php-7.3.7.tar.gz && \
    tar zxf php-7.3.7.tar.gz && \
    cd php-7.3.7 && \
    ./configure --prefix=/usr/local/php \
    --with-config-file-path=/usr/local/php/etc \
    --enable-fpm --enable-opcache \
    --with-mysql --with-mysqli --with-pdo-mysql \
    --with-openssl --with-zlib --with-curl --with-gd \
    --with-jpeg-dir --with-png-dir --with-freetype-dir \
    --enable-mbstring --with-mcrypt --enable-hash && \
    make -j 4 && make install && \
    cp php.ini-production /usr/local/php/etc/php.ini && \
    cp sapi/fpm/php-fpm.conf /usr/local/php/etc/php-fpm.conf && \
    sed -i "90a \daemonize = no" /usr/local/php/etc/php-fpm.conf && \
    mkdir /usr/local/php/log && \
    cd / && rm -rf php* && \
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

ENV PATH $PATH:/usr/local/php/sbin:/usr/local/php/bin
COPY php.ini /usr/local/php/etc/
COPY php-fpm.conf /usr/local/php/etc/
WORKDIR /usr/local/php
EXPOSE 9000
CMD ["php-fpm"]

```







### Redis-Yaml

```yaml
  ### Redis container #########################################

  redis:
      image: redis:5.0.5
      ports:
        - "${REDIS_PORT}:6379"
      volumes:
        - ./MountData/components/redis/config/redis.conf:/usr/local/etc/redis/redis.conf
        - ./MountData/components/redis/log/redis.log:/var/log/redis/redis.log
      restart: always
      privileged: true
      networks:
        net-lnmp:
          ipv4_address: 10.127.1.5

  ### Tools container #########################################

  tools:
      build:
        context: build/tools
        args:
          TIME_ZONE: ${GLOBAL_TIME_ZONE}
          CHANGE_SOURCE: ${GLOBAL_CHANGE_SOURCE}
      volumes:
        - ./MountData/components/tools/start.sh:/home/start.sh:rw
        - ./MountData/components/tools/backup:/backup:rw
        - ./MountData/components/tools/cron.d:/etc/cron.d:rw
      restart: always
      privileged: true
      networks:
        net-lnmp:
          ipv4_address: 10.127.1.6
```


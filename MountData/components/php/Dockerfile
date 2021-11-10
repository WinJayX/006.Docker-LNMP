FROM centos:centos7.7.1908
MAINTAINER WinJay <WinJayX@Gmail.com>

# you need to install EPEL repository.
RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm  
# remi-release - Configuration de YUM pour le dépôt remi
RUN rpm -Uvh http://rpms.remirepo.net/enterprise/7/remi/x86_64//remi-release-7.7-1.el7.remi.noarch.rpm
# Install remi-release rpm
# RUN rpm -Uvh http://rpms.remirepo.net/enterprise/remi-release-7.rpm

RUN yum -y --nogpgcheck --enablerepo=remi-php74 install php php-fpm php-dba php-gd php-imap php-interbase php-intl \
    php-ldap php-mbstring php-mcrypt php-mysqlnd php-odbc php-pdo_dblib \ 
    php-pear php-pecl-apcu php-pecl-imagick php-pgsql php-recode php-tidy \
    php-bcmath php-pecl-zip php-opcache php-mailparse php-swoole

RUN mkdir -p /run/php-fpm && touch /run/php-fpm/php-fpm.pid && \
    sed -i 's/daemonize = yes/daemonize = no/g' /etc/php-fpm.conf && \
    cp /etc/php.ini /etc/php.ini.original

COPY config/php.ini /etc/
COPY config/www.conf /etc/php-fpm.d/

#设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

WORKDIR /phpapp
EXPOSE 9000
# to run php-fpm foreground by force and allow-to-run-as-root
CMD ["/usr/sbin/php-fpm","-F", "-R"]

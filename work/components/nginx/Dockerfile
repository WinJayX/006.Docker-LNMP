FROM centos:centos7.7.1908
MAINTAINER WinJay <WinJayX@Gmail.com>
ENV TZ=Asia/Shanghai
RUN yum install -y gcc gcc-c++ make openssl-devel pcre-devel
ADD nginx.tar.gz /tmp

RUN cd /tmp/nginx-1.16.1 && \
    ./configure --prefix=/nginxapp && \
    make -j 2 && \
    make install

RUN rm -rf /tmp/nginx* && yum clean all

COPY config/nginx.conf /nginxapp/conf

WORKDIR /nginxapp
EXPOSE 80
CMD ["./sbin/nginx", "-g", "daemon off;"]

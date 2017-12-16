
FROM yangtaihua/ubuntu:latest
MAINTAINER yangtaihua <957651480@qq.com>

#安装nginx
WORKDIR /usr/local/src
#下载并解压源码包
ENV NGINXVERSION nginx-1.12.2
RUN adduser --system --no-create-home --disabled-password --group nginx
#添加www用户和组
RUN groupadd  www&&useradd -g www www
#创建网站根目录
RUN mkdir -p     /var/www/&&chmod -R 775 /var/www/
RUN apt-get  install -y  libpcre3 libpcre3-dev zlib1g-dev \
    && wget "http://nginx.org/download/${NGINXVERSION}.tar.gz"&&tar -zxvf "${NGINXVERSION}.tar.gz" \
    && cd ${NGINXVERSION} \
    && ./configure --prefix=/usr/local/nginx --user=admin --group=admin --with-pcre \
    &&make \
    &&make install

RUN echo "daemon off;" >>/usr/local/nginx/conf/nginx.conf
    #&&echo "daemon off;" >>/usr/local/nginx/conf/nginx.conf
    #&&echo "daemon off;" >>/usr/local/nginx/conf/nginx.conf
# forward request and error logs to docker log collector
#RUN ln -sf /dev/stdout /var/log/nginx/access.log \
#        && ln -sf /dev/stderr /var/log/nginx/error.log
WORKDIR /var/www
#清理包
RUN apt-get autoclean \
    && apt-get autoremove \
    && rm -rf /var/lib/apt/lists/*
#设置生成容器时需要执行的脚本
ADD run.sh /root/run.sh
RUN chmod u+x /root/run.sh
CMD ["/root/run.sh"]
#开放22、80、443端口
EXPOSE 22
EXPOSE 80
EXPOSE 443
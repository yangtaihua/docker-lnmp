# sd-swoole
FROM php:7.3-fpm
MAINTAINER yangtaihua 957651480@qq.com
#apt-get安装依赖时，可能会有对话框，制作镜像时如果不选择会导致失败，解决办法也很简单
ENV DEBIAN_FRONTEND noninteractive
#修改root 密码
RUN echo  "root:root" | chpasswd
#更新安装基本命令
RUN apt-get update&&apt-get install -y wget git  --no-install-recommends

#ssh服务安装
COPY sshd_config /etc/ssh/
RUN apt-get install -y openssh-server&& mkdir -p /var/run/sshd && mkdir -p /var/log/sshd

# supervisor
RUN apt-get install -y supervisor&&mkdir -p /var/log/supervisor

#安装PHP扩展所需依赖包
RUN apt-get install -y zlib1g-dev libzip-dev libssl-dev \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev

#docker 方式安装扩展
RUN docker-php-ext-install  opcache bcmath pdo_mysql zip sockets
RUN docker-php-ext-enable opcache
#gd安装
RUN  docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd
#下载编译的源码包
RUN	 cd /home && rm -rf temp && mkdir temp && cd temp \
	&& wget https://github.com/swoole/swoole-src/archive/v4.3.1.tar.gz \
	&& tar -xzvf v4.3.1.tar.gz

#phalcon
#RUN cd /home/temp/cphalcon-3.4.3 && phpize && make && make install && docker-php-ext-enable phalcon
#swoole
RUN cd /home/temp/swoole-src-4.3.1 && phpize \
    && ./configure \
            --with-php-config=/usr/local/bin/php-config \
    	    --enable-async-redis \
    	    --enable-openssl \
    	    --enable-http2 \
    	    ##--enable-sockets \
    	    --enable-mysqlnd \
    && make && make install && docker-php-ext-enable swoole
#pecl方式安装扩展
#RUN pecl install yaf&&  docker-php-ext-enable yaf
#xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug
#composer 安装
RUN php -r"copy('https://getcomposer.org/installer','composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/bin --filename=composer \
    && composer config -g repo.packagist composer https://packagist.phpcomposer.com
#清除下载的临时包
RUN rm -rf /home/temp
#清除pecl下载的包
RUN rm -rf /tmp/pear/*
#清除无用的包
RUN apt-get autoclean \
    && apt-get autoremove \
    && rm -rf /var/lib/apt/lists/*
#设置生成容器时需要执行的脚本
EXPOSE 22
EXPOSE 9000
#配置supervisor
RUN echo "[supervisord]">> /etc/supervisor/conf.d/supervisord.conf \
    &&echo "nodaemon=true">> /etc/supervisor/conf.d/supervisord.conf \
    &&echo "[program:sshd]">> /etc/supervisor/conf.d/supervisord.conf \
    &&echo "command=/usr/sbin/sshd -D">> /etc/supervisor/conf.d/supervisord.conf
#设置生成容器时需要执行的脚本
RUN echo "[program:php-fpm]">> /etc/supervisor/conf.d/supervisord.conf \
    &&echo "command=php-fpm">> /etc/supervisor/conf.d/supervisord.conf
#设置生成容器时需要执行的脚本
CMD ["/usr/bin/supervisord"]


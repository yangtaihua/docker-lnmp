# sd-swoole
FROM php:7.1-fpm
MAINTAINER yangtaihua 957651480@qq.com


#修改root 密码
RUN echo  "root:root" | chpasswd

#更新安装基本命令
RUN apt-get update&&apt-get install -y \
    unzip \
    wget \
    git \
    --no-install-recommends

#ssh服务安装
COPY sshd_config /etc/ssh/
RUN apt-get install -y openssh-server&& mkdir -p /var/run/sshd && mkdir -p /var/log/sshd

# supervisor
RUN apt-get install -y supervisor&&mkdir -p /var/log/supervisor

#安装PHP扩展所需依赖包
RUN apt-get install -y zlib1g-dev \
    libpng12-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libssl-dev

#docker 方式安装扩展
RUN  docker-php-ext-configure gd \
        --with-gd \
        --with-freetype-dir=/usr/include/ \
        --with-png-dir=/usr/include/ \
        --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-install sockets \
    && docker-php-ext-install zip \
    && docker-php-ext-install  opcache bcmath pdo_mysql

#下载编译的源码包

RUN	 cd /home && rm -rf temp && mkdir temp && cd temp \
	&& wget https://github.com/swoole/swoole-src/archive/v4.0.1.tar.gz \
	https://github.com/nghttp2/nghttp2/releases/download/v1.32.0/nghttp2-1.32.0.tar.xz \
	https://github.com/redis/hiredis/archive/v0.13.3.tar.gz \
	https://github.com/phpredis/phpredis/archive/3.1.3.tar.gz \
	&& tar -xzvf 3.1.3.tar.gz \
	&& tar -xzvf v0.13.3.tar.gz \
	&& tar -xvf nghttp2-1.32.0.tar.xz \
	&& tar -xzvf v4.0.1.tar.gz

#编译源码包
RUN cd /home/temp/nghttp2-1.32.0 \
    && ./configure \
    && make && make install \
    && cd /home/temp/hiredis-0.13.3 \
	&& make -j && make install && ldconfig \
	&& cd /home/temp/swoole-src-4.0.1 \
	&& phpize \
	&& ./configure \
        --with-php-config=/usr/local/bin/php-config \
	    --enable-async-redis \
	    --enable-openssl \
	    --enable-http2 \
	    ##--enable-sockets \
	    --enable-mysqlnd \
	&& make && make install
RUN	cd /home/temp/phpredis-3.1.3 \
    #&& pecl install igbinary \
	&& phpize \
	#&& ./configure --enable-redis-igbinary \
	&& ./configure  \
	&& make &&  make install \
	&& cd /home/temp \
	&& rm -rf /home/temp

#pecl方式安装扩展
ENV XDEBUG_HOST localhost
RUN pecl install inotify \
    && pecl install yaf \
    && pecl install xdebug \
    && cd /usr/local/etc/php/conf.d/ \
    && echo extension=inotify.so>inotify.ini \
    && echo extension=yaf.so>yaf.ini \
    && echo zend_extension=xdebug.so>xdebug.ini \
    && echo xdebug.enable = on>>xdebug.ini \
    && echo xdebug.remote_enable = on>>xdebug.ini \
    && echo xdebug.remote_host =${XDEBUG_HOST}>>xdebug.ini \
    && echo xdebug.idekey=PHPSTORM>>xdebug.ini \
    && echo xdebug.remote_handler = dbgp>>xdebug.ini \
    && echo xdebug.remote_port = 9001>>xdebug.ini \
    && echo extension=igbinary.so>igbinary.ini \
    && echo extension=swoole.so>swoole.ini
#composer 安装
RUN php -r"copy('https://getcomposer.org/installer','composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/bin --filename=composer \
    && composer config -g repo.packagist composer https://packagist.phpcomposer.com
#清除无用的包
RUN apt-get autoclean \
    && apt-get autoremove \
    && rm -rf /var/lib/apt/lists/*

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


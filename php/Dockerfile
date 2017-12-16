
FROM yangtaihua/ubuntu:14.04
MAINTAINER yangtaihua <957651480@qq.com>

#添加www用户和组
RUN groupadd  www&&useradd -g www www
#创建网站根目录
RUN mkdir -p     /var/www/&&chmod -R 775 /var/www
WORKDIR /usr/local/src
ENV PHPVERSION php-7.1.11
#更新源
RUN apt-get update && apt-get install -y  make unzip gdb autoconf \
    build-essential libexpat1-dev libgeoip-dev libpng-dev \
    libpcre3-dev libssl-dev libxml2-dev rcs zlib1g-dev \
    libmcrypt-dev libcurl4-openssl-dev libjpeg-dev \
    libpng-dev libwebp-dev pkg-config\
    openssl \
	libssl-dev \
	--no-install-recommends
#php7 编译报错
RUN  cp /usr/lib/x86_64-linux-gnu/libssl.so  /usr/lib
#安装php
RUN mkdir /etc/php&&mkdir /etc/php/conf.d
RUN apt-get update &&apt-get install -y  \
    &&wget -O ${PHPVERSION}.tar.gz "http://cn2.php.net/get/${PHPVERSION}.tar.gz/from/this/mirror" \
    &&tar -zxvf ${PHPVERSION}.tar.gz \
    &&cd ${PHPVERSION} \
    &&./configure --prefix=/usr/local/php \
    --with-config-file-path=/etc/php \
    --with-config-file-scan-dir=/etc/php/conf.d \
    --enable-debug \
    --enable-fpm \
    --with-fpm-user=www \
    --with-fpm-group=www \
    --with-curl \
    --with-gd \
    --with-gettext \
    --with-iconv-dir \
    --with-libdir=lib64 \
    --with-libxml-dir \
    --with-mysql \
    --with-mysqli \
    --with-openssl \
    --with-pdo-mysql \
    --with-pdo-sqlite \
    --with-pear \
    --with-png-dir \
    --with-xmlrpc \
    --with-zlib \
    --enable-fpm \
    --enable-bcmath \
    --enable-libxml \
    --enable-mbstring \
    --enable-opcache \
    --enable-pcntl \
    --enable-shmop \
    --enable-soap \
    --enable-sockets \
    --enable-sysvsem \
    --enable-xml \
    --enable-zip \
    &&make&&make install \
    &&cp php.ini-development /etc/php/php.ini \
    &&cd /usr/local/php/etc&&cp php-fpm.conf.default php-fpm.conf \
    &&cd  php-fpm.d&&cp www.conf.default www.conf
    #&&echo "export PATH=/usr/local/php/bin:\$PATH" >> ~/.bashrc \
    #&&echo "export PATH=/usr/local/php/sbin:\$PATH" >> ~/.bashrc \
    #&&source ~/.bashrc

#安装swoole
ENV PATH="/usr/local/php/bin:/usr/local/php/sbin:${PATH}"
RUN cd /home && rm -rf temp && mkdir temp && cd temp \
	&& wget https://github.com/swoole/swoole-src/archive/v1.9.19.tar.gz \
	https://github.com/redis/hiredis/archive/v0.13.3.tar.gz \
	https://github.com/phpredis/phpredis/archive/3.1.3.tar.gz \
	&& tar -xzvf 3.1.3.tar.gz \
	&& tar -xzvf v0.13.3.tar.gz \
	&& tar -xzvf v1.9.19.tar.gz \
	&& cd /home/temp/hiredis-0.13.3 \
	&& make -j && make install && ldconfig \
	&& cd /home/temp/swoole-src-1.9.19 \
	&& phpize && ./configure --enable-async-redis --enable-openssl && make \
	&& make install \
	&& pecl install inotify \
	&& pecl install ds \
	&& pecl install igbinary \
	&& cd /home/temp/phpredis-3.1.3 \
	&& phpize \
	&& ./configure --enable-redis-igbinary \
	&& make &&  make install \
	&& cd /home/temp \
	&& php -r"copy('https://getcomposer.org/installer','composer-setup.php');" \
	&& php composer-setup.php --install-dir=/usr/bin --filename=composer \
	&& rm -rf /home/temp \
	&&cd /etc/php/conf.d/ \
	&& echo extension=igbinary.so>igbinary.ini \
	&& echo extension=redis.so>redis.ini \
	&& echo extension=inotify.so>inotify.ini \
	&& echo extension=swoole.so>swoole.ini \
	&& echo extension=ds.so>ds.ini \
	&& composer config -g repo.packagist composer https://packagist.phpcomposer.com \
	&& mkdir -p /var/log/supervisor \
	&& apt-get autoclean \
    && apt-get autoremove \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /var/www
#设置生成容器时需要执行的脚本
RUN echo "[program:php-fpm]">> /etc/supervisor/conf.d/supervisord.conf \
    &&echo "command=/usr/local/php/sbin/php-fpm --nodaemonize">> /etc/supervisor/conf.d/supervisord.conf
CMD ["/usr/bin/supervisord"]
EXPOSE 22
EXPOSE 9000
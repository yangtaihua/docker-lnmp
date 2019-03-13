# sd-swoole
FROM php:7.3-fpm
MAINTAINER yangtaihua 957651480@qq.com
#apt-get安装依赖时，可能会有对话框，制作镜像时如果不选择会导致失败，解决办法也很简单
ENV DEBIAN_FRONTEND noninteractive

#更新安装基本命令
RUN apt-get update&&apt-get install -y wget  --no-install-recommends

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
	&& wget https://github.com/swoole/swoole-src/archive/v4.2.12.tar.gz \
	&& tar -xzvf v4.2.12.tar.gz

#swoole
RUN cd /home/temp/swoole-src-4.2.12 && phpize && ./configure \
        --with-php-config=/usr/local/bin/php-config \
	    --enable-openssl \
	    ##--enable-sockets \
	    --enable-mysqlnd \
	&& make && make install && echo "extension=swoole.so">>/usr/local/etc/php/php.ini

#pecl方式安装扩展
#RUN pecl install yaf&& echo "extension=yaf.so">>/usr/local/etc/php/php.ini \
#xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug \
    && echo "xdebug.remote_enable=1" >> /usr/local/etc/php/php.ini
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
CMD ["php-fpm"]


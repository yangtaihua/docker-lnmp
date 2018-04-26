# sd-swoole
FROM php:7.1-fpm
MAINTAINER yangtaihua 957651480@qq.com
RUN echo  "root:root" | chpasswd
#ssh服务安装
COPY sshd_config /etc/ssh/
# 构建swoole环境，在这里安装了php,swoole,composer
RUN apt-get update && apt-get install -y \
	zlib1g-dev \
	libpng12-dev \
	libjpeg-dev \
	libfreetype6-dev \
	vim \
	libssl-dev \
	unzip \
	wget \
	git \
	make \
	supervisor \
	openssh-server \
	--no-install-recommends \
	&& docker-php-ext-install zip opcache bcmath pdo_mysql \
	&& docker-php-ext-configure gd \
            --with-gd \
            --with-freetype-dir=/usr/include/ \
            --with-png-dir=/usr/include/ \
            --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-enable gd
RUN	 cd /home && rm -rf temp && mkdir temp && cd temp \
	&& wget https://github.com/swoole/swoole-src/archive/v2.1.3.tar.gz \
	https://github.com/redis/hiredis/archive/v0.13.3.tar.gz \
	https://github.com/phpredis/phpredis/archive/3.1.3.tar.gz \
	&& tar -xzvf 3.1.3.tar.gz \
	&& tar -xzvf v0.13.3.tar.gz \
	&& tar -xzvf v2.1.3.tar.gz \
	&& cd /home/temp/hiredis-0.13.3 \
	&& make -j && make install && ldconfig \
	&& cd /home/temp/swoole-src-2.1.3 \
	&& phpize && ./configure --enable-async-redis --enable-openssl && make \
	&& make install \
	&& pecl install inotify \
	&& pecl install yaf \
	&& pecl install xdebug \
	&& pecl install igbinary \
	&& cd /home/temp/phpredis-3.1.3 \
	&& phpize \
	&& ./configure --enable-redis-igbinary \
	&& make &&  make install \
	&& cd /home/temp \
	&& php -r"copy('https://getcomposer.org/installer','composer-setup.php');" \
	&& php composer-setup.php --install-dir=/usr/bin --filename=composer \
	&& rm -rf /home/temp \
	&& cd /usr/local/etc/php/conf.d/ \
	&& echo extension=igbinary.so>igbinary.ini \
	&& echo extension=redis.so>redis.ini \
	&& echo extension=inotify.so>inotify.ini \
	&& echo extension=swoole.so>swoole.ini \
	&& echo extension=yaf.so>yaf.ini \
	&& echo zend_extension=xdebug.so>xdebug.ini \
	&& composer config -g repo.packagist composer https://packagist.phpcomposer.com \
	&& mkdir -p /var/log/supervisor \
	&& mkdir -p /var/run/sshd \
	&& mkdir -p /var/log/sshd \
	&& apt-get autoclean \
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


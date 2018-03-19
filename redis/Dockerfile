
FROM registry.cn-shenzhen.aliyuncs.com/yangtaihua/ubuntu:14.04
MAINTAINER yangtaihua <957651480@qq.com>
ENV REDISVERSION redis-4.0.8
RUN cd /home && rm -rf temp && mkdir temp && cd temp
RUN apt-get update &&wget -O ${REDISVERSION}.tar.gz "http://download.redis.io/releases/${REDISVERSION}.tar.gz" \
    && tar -zxvf ${REDISVERSION}.tar.gz \
    && mkdir /usr/local/redis && mkdir -p /usr/local/redis/conf \
    && cd ${REDISVERSION} && make && cp redis.conf /usr/local/redis/conf \
    && cd src && make install PREFIX=/usr/local/redis

ENV PATH="/usr/local/redis/bin/redis-server:/usr/local/redis/bin/redis-cli:${PATH}"
#设置生成容器时需要执行的脚本
RUN echo "[program:redis]">> /etc/supervisor/conf.d/supervisord.conf \
    &&echo "nodaemon=true">> /etc/supervisor/conf.d/supervisord.conf \
    &&echo "command=/usr/local/redis/bin/redis-server /usr/local/redis/conf/redis.conf">> /etc/supervisor/conf.d/supervisord.conf
EXPOSE 22
EXPOSE 6379
CMD ["/usr/bin/supervisord"]

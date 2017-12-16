FROM ubuntu:14.04
MAINTAINER yangtaihua <957651480@qq.com>
# 更新源，原生ubuntu镜像中的下载源速度太慢
RUN  cp /etc/apt/sources.list /etc/apt/sources.list.back \
    && sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/mirrors\.163\.com\/ubuntu\//g' /etc/apt/sources.list
#apt-get安装依赖时，可能会有对话框，制作镜像时如果不选择会导致失败，解决办法也很简单
ENV DEBIAN_FRONTEND noninteractive
#更新，安装常用的命令和依赖
RUN  apt-get update&&apt-get install -y vim wget gcc make
#升级linux 内核
#ssh服务安装
COPY sshd_config /etc/ssh/
RUN  apt-get install -y openssh-server
# 下面这两句比较特殊，在centos6上必须要有，否则创建出来的容器sshd不能登录
RUN ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
RUN mkdir /var/run/sshd
#修改root密码
RUN echo  "root:root" | chpasswd
# 添加测试用户admin，密码admin，并且将此用户添加到sudoers里
RUN useradd admin&&mkdir /home/admin \
    &&echo "admin:admin" | chpasswd \
    &&echo "admin   ALL=(ALL)       ALL" >> /etc/sudoers
#开放22端口
EXPOSE 22
#添加ssh用户
#设置生成容器时需要执行的脚本
CMD ["/usr/sbin/sshd","-D"]







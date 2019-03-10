FROM nginx:1.13
#添加hosts
RUN echo "admin.app.com company.app.com api.app.com app.com ">>/etc/hosts
#创建目录
RUN mkdir -m 777 -p /var/www/html
WORKDIR /var/www/html
CMD ["nginx", "-g", "daemon off;"]
###关于Docker
  首先需要掌握[docker](https://docs.docker.com) 和 [Docker-compose](https://docs.docker.com/compose)相关知识
    
### 构建思路
  docker支持直接拉取镜像的方式或者通过dockerfile的方式构建容器，
  前者虽然官方镜像，轻量，但是没有源码包或者相关的配置文件的 
  如redis镜像是没有相关的配置文件，就需要使用后者的方式自行构建容器
  首先构建一个更新源的基础镜像，为后续的构建节省更新源的时间
  
### 开始

**构建步骤:**

1. 公共基础镜像制作(docker for windows10)

    docker-compose build ./ubuntu -t yangtaihua/ubuntu   
2. 通过公共镜像构建其他镜像 

    docker-compose build
3. 启动容器

    docker-compose up
### 阿里云镜像地址
    
### **注意事项**
  服务的配置文件请通过winscp连接的方式进行拷贝到本地，再进行共享
  当在windows上构建镜像时shell脚本必须是linux格式
  构建的容器CMD不能可终止的，不然会导致容器启动后就退出了  
### Contributors
yangtaihua <957651480@qq.com>
### License

MIT
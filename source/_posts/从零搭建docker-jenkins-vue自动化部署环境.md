---
title: 从零搭建docker+jenkins+vue自动化部署环境
date: 2020-07-29 21:47:43
tags:
categories:
---

## 1、docker部分
### 1.1、docker简介
Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口

### 1.2、docker架构
简单的说，docker就是一个轻量级的linux系统。Docker 容器通过 Docker 镜像来创建。容器与镜像的关系类似于面向对象编程中的对象与类。docker架构如图所示：
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729185837.png)

### 1.3、docker虚拟机管理命令
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729185955.png)

### 1.4、安装docker

更新软件库
```
yum update -y
```

安装docker
```
yum install docker -y
```

### 1.5、启动docker服务
启动docker服务
```
service docker start
```
其它相关命令
```
service docker restart  // 重启docker服务
service docker stop     // 停止docker服务
```
<!-- more -->
## 2、jenkins部分

- 查询jenkins镜像
```
docker search jenkins 
```

- 拉取最新的jenkins镜像
```
docker pull jenkins:latest
```


- 启动jenkins
```
sudo docker run -d -u 0 --privileged  --name jenkins_node1 -p 5000:8080 -v /root/jenkins:/var/jenkins_home jenkins:latest

<!-- docker run -d -p 80:8080 -p 50000:50000 -v jenkins:/var/jenkins_home -v /etc/localtime:/etc/localtime --name jenkins jenkins:latest -->

```

这里逐条解释下各参数的意义。
- `-u 0`: 指的是传入root账号ID，覆盖容器中内置的账号
- `v /root/jenkins:/var/jenkins_home` : 指的是 将docker容器内的目录/var/jenkins_home映射到宿主机 /root/jenkins目录上
- `--name jenkins` : 将容器命名为 jenkins
- `-p 5000:8080` : 端口映射，将容器的8080端口映射到宿主机的5000端口
- `--privileged` : 赋予最高权限

运行一个镜像为jenkins:latest的容器，命名为jenkins，使用root账号覆盖容器中的账号，赋予最高权限，将容器的
/var/jenkins_home映射到宿主机的
/root/jenkins目录下，映射容器中8080端口到宿主机5000端口

启动后输入命令docker ps -a查看所有容器，可以看到jenkins已成功启动。



不出意外你现在可以打开你的服务器地址 http://xxxxxxx:端口号 就能看到这个界面：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729214950.png)


打开你所创建的jenkins目录进入到jenkins-home
/home/jenkins/jenkins-home

进入 secrets 目录
```
cat initialAdminPassword
```

然后把里面的文本复制出来填到管理员密码中

接下来需要安装两个插件
```
NodeJS Plugin
Publish Over SSH
```

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729215127.png)

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729215146.png)

然后我们滑到最下方

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729215328.png)

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729215413.png)

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729215434.png)


## 开始创建项目部署

[官方参考](https://cli.vuejs.org/guide/deployment.html#bitbucket-cloud "官方参考")


### 1、首先在项目根目录创建一个nginx.conf文件
```
user  nginx;
worker_processes  1;
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;
events {
  worker_connections  1024;
}
http {
  include       /etc/nginx/mime.types;
  default_type  application/octet-stream;
  log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
  '$status $body_bytes_sent "$http_referer" '
  '"$http_user_agent" "$http_x_forwarded_for"';
  access_log  /var/log/nginx/access.log  main;
  sendfile        on;
  keepalive_timeout  65;
  server {
    listen       80;
    server_name  www.lovelp.xin;  # 域名
    location / {
      root   /app;  # 指向目录
      index  index.html;
      try_files $uri $uri/ /index.html;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
      root   /usr/share/nginx/html;
    }
  }
}

```

### 2、在项目根目录创建一个Dockerfile文件
```
FROM node
WORKDIR /app

COPY package*.json ./
RUN npm install -g cnpm --registry=https://registry.npm.taobao.org
RUN cnpm install
COPY ./ /app
RUN npm run build:prod

FROM nginx
RUN mkdir /app
COPY --from=0 /app/dist /app
COPY nginx.conf /etc/nginx/nginx.conf
```
### 3、在项目根目录创建一个setup.sh来帮助我们执行脚本
```
#!/usr/bin/env bash
image_version=`date +%Y%m%d%H%M`;
# 关闭shop_admin容器
docker stop shop_admin || true;
# 删除shop_admin容器
docker rm shop_admin || true;
# 删除shop/admin镜像
docker rmi --force $(docker images | grep shop/admin | awk '{print $3}')
# 构建shop/admin:$image_version镜像
docker build . -t shop/admin:$image_version;
# 查看镜像列表
docker images;
# 基于shop/admin 镜像 构建一个容器 shop_admin
docker run -p 9527:80 -d --name shop_admin shop/admin:$image_version;
# 查看日志
docker logs shop_admin;
#删除build过程中产生的镜像    #docker image prune -a -f
docker rmi $(docker images -f "dangling=true" -q)
# 对空间进行自动清理
docker system prune -a -f

```

###　4、最后我们需要在jenkins上创建项目
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729215944.png)

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729220004.png)

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729220029.png)

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729220042.png)

最后我们就可以愉快的 Build Now 了


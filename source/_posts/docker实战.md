---
title: docker实战
date: 2020-07-29 17:49:01
tags:
categories:
---
## 镜像与容器

Docker 中有两个重要概念。

一个是容器（Container）：容器特别像一个虚拟机，容器中运行着一个完整的操作系统。可以在容器中装 `Nodejs`，可以执行`npm install`，可以做一切你当前操作系统能做的事情

另一个是镜像（Image）：镜像是一个文件，它是用来创建容器的。如果你有装过 Windows 操作系统，那么 Docker 镜像特别像“Win7纯净版.rar”文件

上边就是你所需要了解的 Docker 全部基础知识。就这么简单

在 Docker 中，我们通常称你当前使用的真实操作系统为**宿主机(Host)**

## 安装 Docker
安装 Docker 在你的电脑上就像安装 VS Code 一样简单

**Linux**：get.docker.com/
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```
<!-- more -->
## 运行 Docker

接下来我们搭建一个能够托管静态文件的 Nginx 服务器

容器运行程序，而容器哪来的呢？容器是镜像创建出来的。那镜像又是哪来的呢？

镜像是通过一个 Dockerfile 打包来的，它非常像我们前端的`package.json`文件

所以创建关系为：
```
Dockerfile: 类似于“package.json”
 |
 V
Image: 类似于“Win7纯净版.rar”
 |
 V
Container: 一个完整操作系统
```

### 创建文件
我们创建一个目录`hello-docker`，在目录中创建一个`index.html`文件，内容为：
```
<h1>Hello docker</h1>
```
然后再在目录中创建一个`Dockerfile`文件，内容为：
```
FROM nginx

COPY ./index.html /usr/share/nginx/html/index.html

EXPOSE 80
```
此时，你的文件结构应该是：
```
hello-docker
  |____index.html
  |____Dockerfile
```
### 打包镜像
文件创建好了，现在我们就可以根据Dockerfile创建镜像了！
```
<!-- 打包镜像 -->
docker image build ./ -t hello-docker:1.0.0 
```
`docker image build ./ -t hello-docker:1.0.0`的意思是：基于路径`./`（当前路径）打包一个镜像，镜像的名字是`hello-docker`，版本号是`1.0.0`。该命令会自动寻找`Dockerfile`来打包出一个镜像

> Tips: 你可以使用docker images来查看本机已有的镜像

不出意外，你应该能得到如下输出：
```
Sending build context to Docker daemon  3.072kB
Step 1/3 : FROM nginx
 ---> 5a3221f0137b
Step 2/3 : COPY ./index.html /usr/share/nginx/html/index.html
 ---> 1c433edd5891
Step 3/3 : EXPOSE 80
 ---> Running in c2ff9ec2e945
Removing intermediate container c2ff9ec2e945
 ---> f6a472c1b0a0
Successfully built f6a472c1b0a0
Successfully tagged hello-docker:1.0.0

```
可以看到其运行了 Dockerfile 中的内容，现在我们简单拆解下：

- `FROM nginx`：基于哪个镜像
- `COPY ./index.html /usr/share/nginx/html/index.html`：将宿主机中的./index.html文件复制进容器里的/usr/share/nginx/html/index.html
- `EXPOSE 80`：容器对外暴露80端口

### 运行容器
我们刚刚使用 Dockerfile 创建了一个镜像。现在有镜像了，接下来要根据镜像创建容器：
```
docker container create -p 2333:80 hello-docker:1.0.0
docker container start xxx # xxx 为上一条命令运行得到的结果

```

在上边第一个命令中，我们使用`docker container create`来创建基于`hello-docker:1.0.0`镜像的一个容器，使用`-p`来指定端口绑定——将容器中的80端口绑定在宿主机的2333端口。执行完该命令，会返回一个容器ID
而第二个命令，则是启动这个容器
启动后，就能通过访问本机的2333端口来达到访问容器内80端口的效果了

然后在浏览器打开`127.0.0.1:2333`，你应该能看到刚刚自己写的index.html内容

> Tips: 你可以使用`docker containers ls` 或者 `docker ps`来查看当前运行的容器

用 `docker run` 可以一步到位创建并运行一个容器，然后进入该容器。
```
docker run -d -p 2333:80 hello-docker
```

### 进入容器
当容器运行后，可以通过如下命令进入容器内部：

```
docker container exec -it xxx /bin/bash # xxx 为容器ID
```

原理实际上是启动了容器内的`/bin/bash`，此时你就可以通过`bash shell`与容器内交互了。就像远程连接了SSH一样
 

### 发生了什么
我们总结下都发生了什么：
- 写一个 Dockerfile
- 使用docker image build来将Dockerfile打包成镜像
- 使用docker container create来根据镜像创建一个容器
- 使用docker container start来启动一个创建好的容器


## 部署node项目-例子1
使用docker部署项目主要分为三步

**1.创建nodejs项目**
编写`package.json`为项目安装依赖所用
```
{
  "name": "expressPro",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.16.3"
  }
}

```
服务启动文件`server.js`
``` js
var express = require('express');
var app = express();

app.use(express.static('dist'));//表示dist目录下的为静态资源

var server = app.listen(3333, function () {
  var host = server.address().address;
  var port = server.address().port;

  console.log('Example app listening at http://%s:%s', host, port);
});
```
至此我们s本地完成了nodejs项目开启一个简单web服务的功能

**2.编写Dockerfile以及dockerignore**

- Dockerfile
``` js
#引用镜像
FROM node:8.9.4-alpine
#作者
MAINTAINER mkq
#执行命令，创建文件夹
RUN mkdir -p /usr/src/workPlace_express/expressPro

#将dist目录拷贝到镜像里
COPY ./ /usr/src/workPlace_express/expressPro/

#指定工作目录
WORKDIR /usr/src/workPlace_express/expressPro


#安装依赖及构建node应用
RUN npm install
#配置环境变量
 ENV HOST 0.0.0.0
 ENV PORT 3333
#定义程序默认端口
EXPOSE 3333
#运行程序命令
CMD ["node","server.js"]

```

**3.打包镜像**
在Dockerfile同级路径下执行如下命令构建镜像
```
docker image build ./ -t docker_demo1:1.0.0 
```
不出意外的话我们就构建docker镜像成功了。然后可以使用如下命令查看已构建的镜像
```
docker images
```
镜像构建完成之后我们就可以通过镜像启动一个容器了，使用如下命令
```
docker run -d -p 9000:3333 docker_demo1
```
-d表示后台运行。-p表示指定端口映射，此例中是将宿主机器的9000端口映射到3333,3333端口也就是我们的Dockerfile对外暴露的接口。
然后我们可以使用如下命令查看已启动的容器
```
docker ps
```
至此已经用docker部署了一个node应用。我们便可以通过宿主主机的ip:9000访问到容器上3333端口上的服务了。

##  使用 PM2 在 Docker 中部署 Node 应用
平时，我们部署 Node 应用时，都会使用 PM2  这样的进程管理工具。那么在 Node 中如何集成它们呢？Dockfile如下：

```
# Dockerfile

FROM node:carbon                  # 引入Node
COPY . /node-project              # 把当前目录所有内容拷贝到目录/node-project。拷贝内容受.dockerignore影响
WORKDIR /node-project             # 指定容器的工作目录为 /node-project 
RUN npm install pm2 -g            # 全局安装 pm2
RUN npm install                   # 安装项目依赖
EXPOSE 3001                       # 曝露容器端口
CMD ["pm2-runtime", "./app.js"]   # 执行启动命令
```

构建好 image 后，假设构建的 image 为 pm2-demo:0.0.1，则使用下列命令启动容器即可：

```
docker container run --name server-pm2-demo -p 4001:3001 -it pm2-demo:0.0.1
```

上面的 "-p 4001:3001" 表示把宿主的 4001 端口和容器的 3001 端口关联起来，外部访问时，记得用 4001 端口。这样我们就成功使用 PM2 部署了我们的 Node 应用。


## 部署vue项目


























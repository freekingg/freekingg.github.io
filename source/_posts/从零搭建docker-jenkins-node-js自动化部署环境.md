---
title: 从零搭建docker+jenkins+node.js自动化部署环境
date: 2020-07-29 18:57:02
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

<!-- more -->

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

## node部分

### 1.1、创建koa项目
通过koa框架写一个hello-world项目
app.js
```
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000);
```
### 1.2、创建Dockerfile

然后在项目的根目录下新建一个Dockerfile文件

Dockerfile 是一个文本文件，其内包含了一条条的指令(Instruction)，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。

Dockerfile
``` yaml
# Dockerfile
# 使用node做为镜像
FROM node
# 在容器中创建该目录
RUN mkdir -p /home/project
# 设置容器的工作目录为该目录
WORKDIR /home/project 
# 向外提供3000端口
EXPOSE 3000
# 容器创建完成后执行的命令
CMD npm install --registry=https://registry.npm.taobao.org && node ./start.js
```

将该项目发布到github上，为之后的jenkins部署做前提准备

## 3、jenkins部分

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
sudo docker run -d -u 0 --privileged  --name myjenkins -p 5000:8080 -v /root/myjenkins:/var/jenkins_home jenkins

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


- 配置Jenkins

在浏览器输入http://ip:5000进入Jenkins登录页面。页面会提示你到服务器的指定位置获取初始化密码

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729193457.png)

**获取密码**

```
cat /root/jenkins/secrets/initialAdminPassword
```

**在页面输入密码，点击Continue，进入插件安装页面。我们点击左边的Install suggested plugins，安装推荐插件就好。**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729193708.png)

**安装好插件后，系统会提示建立管理员账户。**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729193738.png)


**输入账号密码信息后点击保存（信息要填完），来到首页**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729194719.png)

**配置jenkins，进入系统管理页面，管理插件**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729194739.png)

**选择 ssh插件，**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729194803.png)

直接安装，等待安装完成，回到首页，

**来到 系统管理 -> 系统配置**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729194829.png)

**拖到最下Publish over SSH这**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729194904.png)

**选择高级，输入服务器IP，用户名，密码，然后点击 Test Configuration**

显示 Success 则说明配置没问题。然后保存，回到首页

**新建一个项目**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729194936.png)

**输入项目名称**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729195003.png)


**选择源码管理，使用git管理，输入github仓库地址，添加github用户**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729195138.png)
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729195202.png)
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729195237.png)

**完成来到选择构建环境，**

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200729195259.png)

**执行的命令**
```
sudo docker stop nodeapp || true \
 && sudo docker rm nodeapp || true \
 && cd /root/jenkins_node1/workspace/node  \
 && sudo docker build --rm --no-cache=true  -t node  - < Dockerfile \
 && sudo docker run -d --privileged=true --name nodeapp -p 3000:3000 -v /root/jenkins_node1/workspace/node:/home/project node
```

**保存后，点击立即构建**
构建成功后，可以在宿主机的目录/root/jenkins_node1/workspace/node下看到你的项目文件了

在浏览器输入 docker服务器地址 localhost:3000 即可访问到页面信息

启动失败的，可以查看日志来确定一下失败的原因
```
docker logs nodeapp
```



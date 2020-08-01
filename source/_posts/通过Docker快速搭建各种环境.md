---
title: 通过Docker快速搭建各种环境（全攻略）
date: 2020-07-31 12:24:44
tags:
categories:
---

## Docker 基础教程

### 配置国内镜像加速器

Docker 配置国内镜像加速器，加速下载速度

Docker 默认是从官方镜像地址 Docker Hub 下下载镜像，由于服务器在国外的缘故，导致经常下载速度非常慢。为了提升镜像的下载速度，我们可以手动配置国内镜像加速器，让下载速度飚起来。

国内的镜像加速器选项较多，如：阿里云，DaoCloud 等。

本文主要说说如何配置阿里云的镜像加速器。

#### 一、获取加速信息

- 1、首先你需要注册一个阿里云账号，没有的话，通过下面的连接跳转注册：
https://dev.aliyun.com/

- 2、跳转镜像加速页 https://cr.console.aliyun.com/，获取加速配置信息：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731123229.png)

#### 二、配置 Docker
**确定 Docker Client 版本**

在配置之前，首先需要确定 Docker Client 的版本，推荐是 1.10.0+:

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731123341.png)

**配置镜像加速器**
通过修改 daemon 配置文件 `/etc/docker/daemon.json` 来使用加速器：

执行下面命令：
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://bjtzu1jb.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

#### 三、验证速度
以下载 mongodb 为例，看下速度：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731123517.png)

配置了加速器过后，速度终于飚起来了。


### Docker 下载镜像

#### 一、前言
大家都知道，镜像是 Docker 三大核心概念中最重要的。如果我们想要在本地运行容器，就必须保证本地存在对应的镜像。所以，第一步，我们需要下载镜像。当我们尝试下载镜像时，Docker 会尝试先从默认的镜像仓库（默认使用 Docker Hub 公共仓库）去下载，当然了，用户也可以自定义配置想要下载的镜像仓库。

#### 二、下载镜像
镜像是运行容器的前提，我们可以使用 `docker pull [IMAGE_NAME]:[TAG]`命令来下载镜像，其中 `IMAGE_NAME` 表示的是镜像的名称，而 `TAG` 是镜像的标签，也就是说我们需要通过 “镜像 + 标签” 的方式来下载镜像。

> 注意：您也可以不显式地指定 TAG, 它会默认下载 latest 标签，也就是下载仓库中最新版本的镜像。这里并不推荐您下载 latest 标签，因为该镜像的内容会跟踪镜像的最新版本，并随之变化，所以它是不稳定的。在生产环境中，可能会出现莫名其妙的 bug, 推荐您最好还是显示的指定具体的 TAG。

举个例子，如我们想要下载一个 `Mysql 5.7` 镜像，可以通过命令来下载：

``` shell
docker pull mysql:5.7
```

会看到控制台输出内容如下：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731123820.png)

当有 **Downloaded** 字符串输出的时候，说明下载成功了！！

#### 三、验证镜像
让我们来验证一下，本地是否存在 Mysql5.7 的镜像，运行命令：
```
docker images
```
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731123939.png)

可以看到本地的确存在该镜像，确实是下载成功了！

#### 四、下载镜像相关细节
再说说上面下载镜像的过程：
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731124023.png)

通过下载过程，可以看到，一个镜像一般是由多个层（layer） 组成，类似 f7e2b70d04ae这样的串表示层的唯一 ID。

您可能会想，如果多个不同的镜像中，同时包含了同一个层（layer）,这样重复下载，岂不是导致了存储空间的浪费么?

实际上，Docker 并不会这么傻会去下载重复的层（layer）,Docker 在下载之前，会去检测本地是否会有同样 ID 的层，如果本地已经存在了，就直接使用本地的就好了。

### Docker 查看镜像信息

#### 一、images 命令列出镜像

通过使用如下两个命令，列出本机已有的镜像：

```
docker images
```
或：
```
docker image ls
```
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731124249.png)

对上述红色标注的字段做一下解释：

- **REPOSITORY**: 来自于哪个仓库；
- **TAG**: 镜像的标签信息，比如 5.7、latest 表示不同的版本信息；
- **IMAGE ID**: 镜像的 ID, 如果您看到两个 ID 完全相同，那么实际上，它们指向的是同一个镜像，只是标签名称不同罢了；
- **CREATED**: 镜像最后的更新时间；
- **SIZE**: 镜像的大小，优秀的镜像一般体积都比较小，这也是我更倾向于使用轻量级的 alpine 版本的原因

#### 二、使用 tag 命令为镜像添加标签

通常情况下，为了方便在后续工作中，快速地找到某个镜像，我们可以使用 docker tag 命令，为本地镜像添加一个新的标签。如下图所示：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731124505.png)

为 `docker.io/mysql` 镜像，添加新的镜像标签 `allen_mysql:5.7`。然后使用 `docker images` 命令，查看本地镜像：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731124556.png)

可以看到，本地多了一个 allen_mysql:5.7 的镜像。细心的你一定还会发现，allen_mysql:5.7 和 docker.io/mysql:5.7 的镜像 ID 是一模一样的，说明它们是同一个镜像，只是别名不同而已。

docker tag 命令功能更像是, 为指定镜像添加快捷方式一样。

#### 三、使用 inspect 命令查看镜像详细信息
通过 `docker inspect` 命令，我们可以获取镜像的详细信息，其中，包括创建者，各层的数字摘要等。

```
docker inspect docker.io/mysql:5.7
```

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731124709.png)

docker inspect 返回的是 JSON 格式的信息，如果您想获取其中指定的一项内容，可以通过 -f 来指定，如获取镜像大小：

```
docker inspect -f {{".Size"}} docker.io/mysql:5.7
```

### Docker 搜索镜像

#### 一、search 命令

您可以通过下面命令进行搜索：
```
docker search [option] keyword
```

比如，您想搜索仓库中 mysql 相关的镜像，可以输入如下命令：

```
docker search mysql
```

#### 二、search 子命令

命令行输入 `docker search --help`, 输出如下：

```
Usage:  docker search [OPTIONS] TERM

Search the Docker Hub for images

Options:
  -f, --filter filter   Filter output based on conditions provided
      --help            Print usage
      --limit int       Max number of search results (default 25)
      --no-index        Don't truncate output
      --no-trunc        Don't truncate output
```

可以看到 `search` 支持的子命令有：

- `-f, --filter filter`: 过滤输出的内容；
- `--limit int`：指定搜索内容展示个数;
- `--no-index`: 不截断输出内容；
- `--no-trunc`：不截断输出内容；

比如我们想搜索官方提供的 mysql 镜像，命令如下：
```
docker search --filter=is-offical=true mysql
```

比如，我们想搜索 Stars 数超过 100 的 mysql 镜像：
```
docker search --filter=stars=100 mysql
```

### Docker 删除&清理镜像

#### 一、通过标签删除镜像

以下两种命令均可删除镜像：

```
docker rmi [image]
```

或者：
```
docker image rm [image]
```

支持的子命令如下：

- `-f, -force`: 强制删除镜像，即便有容器引用该镜像；
- `-no-prune`: 不要删除未带标签的父镜像；

例如，我们想删除上章节创建的 allen_mysql:5.7 镜像，命令如下：

```
docker rmi allen_mysql:5.7
```

#### 二、通过 ID 删除镜像

除了通过标签名称来删除镜像，我们还可以通过制定镜像 ID, 来删除镜像，如：

```
docker rmi ee7cbd482336
```
一旦制定了通过 ID 来删除镜像，它会先尝试删除所有指向该镜像的标签，然后在删除镜像本身。

#### 三、删除镜像的限制

删除镜像很简单，但也不是我们何时何地都能删除的，它存在一些限制条件。

当通过该镜像创建的容器未被销毁时，镜像是无法被删除的。


正确的做法应该是：

- 先删除引用这个镜像的容器；
- 再删除这个镜像；


#### 四、清理镜像

我们在使用 Docker 一段时间后，系统一般都会残存一些临时的、没有被使用的镜像文件，可以通过以下命令进行清理：

```
docker image prune
```
它支持的子命令有：

- `-a, --all`: 删除所有没有用的镜像，而不仅仅是临时文件；
- `-f, --force`：强制删除镜像文件，无需弹出提示确认；

另外，执行完 docker image prune 命令后，还是告诉我们释放了多少存储空间！


### Docker 创建镜像

Docker 创建镜像主要有三种：

- 基于已有的镜像创建；
- 基于 Dockerfile 来创建；
- 基于本地模板来导入；

我们将主要介绍常用的 1，2 两种。

#### 一、基于已有的镜像创建

通过如下命令来创建：

```
docker container commit
```

支持的子命令如下：

- `-a, --author=""`: 作者信息；
- `-c, --change=[]`: 可以在提交的时候执行 Dockerfile 指令，如 `CMD、ENTRYPOINT、ENV、EXPOSE、LABEL、ONBUILD、- USER、VOLUME、WORIR` 等；
- `-m, --message=""`: 提交信息；
- `-p, --pause=true`: 提交时，暂停容器运行。

接下来，基于本地已有的 Ubuntu 镜像，创建一个新的镜像：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731125918.png)

首先，让我将它运行起来，并在其中创建一个 test.txt 文件：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731125939.png)

命令如下：

```
docker run -it docker.io/ubuntu:latest /bin/bash
root@a0a0c8cfec3a:/# touch test.txt
root@a0a0c8cfec3a:/# exit
```

创建完 test.txt 文件后，需要记住标注的容器 ID: a0a0c8cfec3a, 用它来提交一个新的镜像(PS: 你也可以通过名称来提交镜像，这里只演示通过 ID 的方式)。

执行命令：
```
docker container commit -m "Added test.txt file" -a "Allen" a0a0c8cfec3a test:0.1
```

提交成功后，会返回新创建的镜像 ID 信息，如下图所示：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731130151.png)


再次查看本地镜像信息，可以看到新创建的 test:0.1 镜像了：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731130309.png)

#### 二、基于 Dockerfile 来创建镜像

通过 Dockerfile 的方式来创建镜像，是最常见的一种方式了，也是比较推荐的方式。Dockerfile 是一个文本指令文件，它描述了是如何基于一个父镜像，来创建一个新镜像的过程。

下面让我们来编写一个简单的 Dockerfile 文件，它描述了基于 Ubuntu 父镜像，安装 Python3 环境的镜像：

``` shell
FROM docker.io/ubuntu:latest

LABEL version="1.0" maintainer="Allen <weiwosuo@github>"

RUN apt-get update && \
    apt-get install -y python3 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

创建完成后，通过这个 Dockerfile 文件，来构建新的镜像，执行命令：

```
docker image build -t python:3 .
```
> 注意：命令的最后有个点，如果不加的话，会构建不成功 ！

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731130453.png)

编译成功后，再次查看本地镜像信息，就可以看到新构建的 python:3 镜像了。

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731130557.png)


### Docker 导出&加载镜像
当我们需要将镜像分享给别人时，可以将镜像导出成 tar 包，别人直接通过加载这个 tar 包，快速地将镜像引入到本地镜像库。

要想使用这两个功能，主要是通过如下两个命令：

- docker save
- docker load

#### 一、导出镜像

查看本地镜像如下：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731130743.png)

例如，我们想要将 python:3 镜像导出来，执行命令：

```
docker save -o python_3.tar python:3
```

执行成功后，查看当前目录：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731130821.png)

可以看到 python_3.tar 镜像文件已经生成。接下来，你可以将它通过复制的方式，分享给别人了！


#### 二、加载镜像
别人拿到了这个 tar 包后，要如何导入到本地的镜像库呢？

通过执行如下命令：

```
docker load -i python_3.tar
```
或者：

```
docker load < python_3.tar
```

导入成功后，查看本地镜像信息，你就可以获得别人分享的镜像了！


## 常用环境安装篇

### Mysql 环境

#### 一、下载 Mysql 镜像

这里以 Mysql 5.7 为例：

```
docker pull mysql:5.7
```
下载完成后，通过 docker images 检查一下镜像是否下载成功:

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731131108.png)


#### 二、正式运行 Mysql 容器：

正式运行 Mysql 容器：

```
docker run -d \
--name mysql \
-p 3306:3306 \
-v /usr/local/docker/mysql/config/mysqld.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf \
-v /usr/local/docker/mysql/data/mysql:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:5.7
```

- `-d`：以后台的方式运行；
- `--name mysql`：指定容器的名称为 mysql;
- `-p 3306:3306` 将容器的 3306 端口挂载到宿主机的 3306 端口上；
- `-e MYSQL_ROOT_PASSWORD=123456`：指定 root 的密码为 123456
- `-v /usr/local/docker/mysql/config/mysqld.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf`: 将容器中 /etc/mysql/mysql.conf.d/mysqld.cnf 配置文件挂载到宿主机的 /usr/local/docker/mysql/config/mysqld.cnf 文件上；
- `-v /usr/local/docker/mysql/data:/var/lib/mysql`: 将容器中 /var/lib/mysql 数据目录挂载到宿主机的 /usr/local/docker/mysql/data 目录下；

执行命令完成后，查看下容器是否启动：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731131907.png)

#### 三、通过 Mysql 客户端连接一下试试

通过 MySQL 客户端连接刚刚创建的 mysql, 看看能否连接成功：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731131944.png)

连接成功了！

#### 相关问题补充

**时区不对问题**

在向数据库插入数据时，总是发现时间比系统时间少 8 个小时，这是因为 Mysql 的时区并不是我们想要的东八区。

解决方案：我们可以在上面说到的，挂载到宿主机的 Mysql 配置文件 mysqld.cnf 中，添加如下参数：

```
default-time-zone = '+08:00'
```

添加改配置后，重启 MySQL 容器, 再插入数据，就发现时间正常了：

```
docker restart your_mysql_container_name
```

### Redis 环境

#### 一、下载 Redis 镜像
首先拉取 Redis 镜像, 这里我选择的是 `redis:alpine` 轻量级镜像版本：
```
docker pull redis:alpine
```
下载完成后，通过 docker images 确认镜像是否已经下载到本地：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731132144.png)

#### 二、运行 Redis 容器

```
docker run \
-p 6379:6379 \
--name redis \
-v /usr/local/docker/redis/redis.conf:/etc/redis/redis.conf \
-v /usr/local/docker/redis/data:/data \
-d redis:alpine \
redis-server /etc/redis/redis.conf --appendonly yes --requirepass "mypassword"
```

命令说明：

- `-p 6379:6379`: 将容器的 6379 端口映射到宿主机的 6379 端口；
- `-v /usr/local/docker/redis/data:/data` : 将容器中的 /data 数据存储目录, 挂载到宿主机中 /usr/local/docker/redis/data 目录下；
- `-v /usr/local/docker/redis/redis.conf:/etc/redis/redis.conf` ： 将容器中 /etc/redis/redis.conf 配置文件，挂载到宿主机的 /usr/local/docker/redis/redis.conf 文件上；
- `redis-server --appendonly yes --requirepass "mypassword"`: 在容器执行 redis-server 启动命令，并打开 redis 持久化配置, 以及设置密码;

命令运行完成后，查看容器是否启动成功：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731132300.png)

#### 三、连接刚刚创建好的容器
执行如下命令，连接 redis：

```
docker run -it redis:alpine redis-cli -h 172.17.0.1
```
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731132330.png)


### MongDB 环境

#### 一、下载 MongoDB 镜像

这里以 mongo 4 版本为例，下载镜像：

```
docker pull mongo:4
```

下载完成后，确认一下镜像是否下载成功:

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731132416.png)

#### 二、运行 MongoDB 镜像
下载成功后，运行 mongoDB 镜像：

```
docker run -d \
--name mongo \
-v /usr/local/docker/mongo/configdb:/data/configdb \
-v /usr/local/docker/mongo/data:/data/db \
-p 27017:27017 \
mongo:4 \
--auth
```

- `-d`: 以后台的方式运行；
- `--name mongo`: 指定容器名称为 mongo;
- `-v /usr/local/docker/mongo/configdb:/data/configdb`: 将容器中 /data/configdb 目录挂载到宿主机的 /usr/local/docker/mongo/configdb 目录下；
- `-v /usr/local/docker/mongo/data:/data/db`: 将容器中 /data/db 数据目录挂载到宿主机的 /usr/local/docker/mongo/data 目录下；
- -p `27017:27017`: 将容器的 27017 端口映射到宿主机的 27017 端口；

执行命令完成后，查看下容器是否启动：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731132541.png)

#### 三、添加管理员账号

执行命令：

```
docker exec -it mongo mongo admin
```

然后，创建一个拥有最高权限 root 账号:

```
db.createUser({ user: 'admin', pwd: '123456', roles: [ { role: "root", db: "admin" } ] });
```

创建成功后，你会看到 Successfully added user:

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731132627.png)


#### 四、用新创建的 root 账户连接，测试一下
```
docker run -it --rm --link mongo:mongo mongo mongo -u admin -p 123456 --authenticationDatabase admin mongo/admin
```
连接成功后，我们可以执行相关 sql：

**显示所有的数据库：**
```
show dbs
```

**使用某个数据库：**
```
use admin
```
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731132734.png)

输入命令 exit，退出连接！

### Ngnix 环境
#### 一、下载 Nginx 镜像
```
docker pull nginx:alpine
```

> PS：我这里用的是 alipne 轻量级的镜像.

下载完成后，通过 docker images 命令检查一下镜像是否下载成功：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731132929.png)

执行：
```
docker run -d \ 
-p 80:80 \ 
--name nginx \
-v /docker/nginx/nginx.conf:/etc/nginx/nginx.conf \
-v /docker/nginx/conf.d:/etc/nginx/conf.d \
-v /docker/nginx/logs:/var/log/nginx \
nginx:alpine 
```

参数说明：

- `-d`: 以后台方式运行镜像；
- `--name`: 指定容器的名称为 nginx;
- `-p 80:80`：将容器的 80 端口映射到主机的 80 端口；
- `-v /docker/nginx/nginx.conf:/etc/nginx/nginx.conf：将容器中的 /etc/nginx/nginx.conf 文件挂载到宿主机中的 /docker/nginx/nginx.conf 文件；
- `-v /docker/nginx/conf.d:/etc/nginx/conf.d`：将容器中 /etc/nginx/conf.d 目录挂载到宿主机中的 conf.d 目录下；
-  `-v /docker/nginx/logs:/var/log/nginx`：将容器中的 /var/log/nginx 目录挂载到宿主机中的 /docker/nginx/logs 目录下, 用以查看 Nginx 日志；

运行命令成功后，直接访问公网 IP 的 80 端口


后续可以在 conf.d/default.config 中添加反向代理相关配置：

```
server {
    listen 443 ssl;
    server_name www.example.top; #你的申请过证书的域名
    client_max_body_size 64M;
    fastcgi_read_timeout 3600;
    error_page   500 502 503 504  /50x.html;
    root   /usr/share/nginx/html;
    try_files $uri $uri/ @rewrite;
    # ssl on;
    ssl_certificate     /etc/nginx/conf.d/certs/www.example.top/example.pem;
    ssl_certificate_key /etc/nginx/conf.d/certs/www.example.top/example.key;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
    ssl_prefer_server_ciphers on;

    location / {
        add_header Content-Security-Policy upgrade-insecure-requests;
        proxy_set_header HOST $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://172.17.0.5:9001/;
    }
}

```


## 通过 Docker 搭建反向代理 Ngnix，并配置 Https SSL 证书
https://www.exception.site/docker/how-to-config-ssl-with-docker-nginx


## 动态设置环境变量

Dockerfile 

```
EXPOSE 30010

ARG node_env # 新增加
ENV NODE_ENV=$node_env  # 新增加
CMD npm run ${NODE_ENV} # 修改
```

下面对上面的代码做个解释

- 通过 `ARG` 指令定义了一个变量，用户可以在构建时通过使用 `--build-arg =` 标志的 `docker build` 命令将其传递给构建器 `ARG node_env`
- 在 Dockerfile 中使用 ENV 引用这个变量` ENV NODE_ENV=$node_env`
- 这一步就是使用了 `CMD npm run ${NODE_ENV}`

剩下的就是在构建镜像时动态传入参数了

```
$ docker image build --build-arg node_env=dev -t my-koa-app . # 构建测试环境
$ docker image build --build-arg node_env=pro -t my-koa-app . # 构建生产环境
```


## 用 Docker 搭建 nodejs 应用

### 创建koa项目
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

### 创建 Dockerfile 文件
```
FROM node:12.16.3-alpine

LABEL maintainer="SvenDowideit@home.org.au"

# 在容器中创建一个目录
RUN mkdir -p /usr/src/testApp/

# 定位到容器的工作目录
WORKDIR /usr/src/testApp/

# RUN/COPY 是分层的，package.json 提前，只要没修改，就不会重新安装包
COPY ./package*.json /usr/src/testApp/

#安装依赖
RUN npm install

# 把当前目录下的所有文件拷贝到 Image 的 /usr/src/testApp/ 目录下
COPY . /usr/src/testApp/

#暴露端口
EXPOSE 3000

#容器启动后执行的命令
CMD node app.js 
```

### 制作镜像
有了 Dockerfile 以后，我们可以运行下面的命令构建前端镜像并命名为 my-koa-app：

```
docker build -t my-koa-app .
```

### 启动容器
最后，让我们从镜像启动容器：

```

docker run \
-p 3006:3000 \
-d --name koaApp \
my-koa-app

```

这样子我们就能从 3006 端口去访问我们的 node 应用。

###  应用运行优化

当然， Node 是公认的不稳定，经常会出现服务器内存溢出，而崩溃退出。

我们针对这一点，可以对 koa 启动命令做优化。引入 pm2 插件，通过 pm2 来启动 express 应用。

> 使用命令 `pm2 start app.js` 之后, pm2 默认在后台运行, 如果使用了 Docker 后,容器运行并立即退出,需要手动给“ pm2 ”指定参数 `--no-daemon`


```
FROM node:12.1.3-alpine

LABEL maintainer="SvenDowideit@home.org.au"

# 在容器中创建一个目录
RUN mkdir -p /usr/src/testApp/

# 定位到容器的工作目录
WORKDIR /usr/src/testApp/

RUN npm install -g pm2

# RUN/COPY 是分层的，package.json 提前，只要没修改，就不会重新安装包
COPY ./package*.json /usr/src/testApp/

#安装依赖
RUN npm install

# 把当前目录下的所有文件拷贝到 Image 的 /usr/src/testApp/ 目录下
COPY . /usr/src/testApp/

#暴露端口
EXPOSE 3000

#容器启动后执行的命令
CMD pm2 start app.js --no-daemon 

```



## 用 Docker 搭建 vue 应用 (案例一)

### 说在前面
网上很多docker部署vue项目的教程，其中很多的文章不乏都是先将vue项目执行npm run build 在本地进行打包，传到自己的仓库去，然后到服务器去拉取我们的代码，获取dist文件，再将该文件挂载到dockr容器内。其实这种操作应当是有缺陷的，我们应当把打包的操作也放到docker的镜像里面去操作。

### 准备好vue项目
```
vue create vueTest
```

### 创建 Dockerfile 文件

```
FROM node:12.16.3-alpine AS builder

# 将容器的工作目录设置为/app(当前目录，如果/app不存在，WORKDIR会创建/app文件夹)
WORKDIR /app 

COPY ./package*.json /app/ 

#安装依赖
RUN npm config set registry "https://registry.npm.taobao.org/" \
  && npm install 
COPY . /app
RUN npm run build 


#指定nginx配置项目，--from=builder 指的是从上一次 build 的结果中提取了编译结果
FROM nginx

#将打包后的文件复制到nginx中
COPY --from=builder app/dist /usr/share/nginx/html/

#用本地的 default.conf 配置来替换nginx镜像里的默认配置。
COPY --from=builder app/nginx.conf /etc/nginx/conf.d/default.conf

#暴露容器80端口
EXPOSE 80

```

可以看到，在这里将打包操作也放到Dokcerfile里面进行操作了。


**该条命令是将我们在镜像里面打包生成的dist文件放进容器内nginx的web目录下面。**
```
COPY --from=builder app/dist /usr/share/nginx/html/
```

**该条命令是将我们项目目录下面的nginx.conf文件复制到容器内nginx的配置文件的目录下面，从而覆盖原有的配置文件。**
```
COPY --from=builder app/nginx.conf /etc/nginx/conf.d/
```

### 创建 nginx.conf 文件
在项目根目录下创建nginx文件，该文件夹下新建文件nginx.conf
```
server {
    listen       80;
    server_name  localhost;

    # 开启 gzip
    gzip  on;
    gzip_min_length 1k;
    gzip_buffers 16 64k;
    gzip_http_version 1.1;
    gzip_comp_level 9;
    gzip_types text/plain application/x-javascript application/javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    gzip_vary on;

    location / {
        # 根目录
        root   /usr/share/nginx/html;
        index  index.html index.htm;

        # 解决HTML5 History 模式
        try_files $uri $uri/ /index.html;
    }
}
```

### 创建 .dockerignore 文件
在项目根目录下创建.dockerignore，用与忽略镜像打包文件
```
 .git
 node_modules
 npm-debug.log
```

### 制作镜像
```
docker image build -t vuetest:1.0 .
```
-t 是给镜像命名 . 是基于当前目录的Dockerfile来构建镜像

### 启动容器
```
docker run \
-p 3000:80 \
-d --name vueTest \
vuetest
```

- `docker run` 基于镜像启动一个容器
- `-p 3000:80` 端口映射，将宿主的3000端口映射到容器的80端口
- `-d` 后台方式运行
- `--name` 容器名 查看 docker 进程

可以发现名为 vueTest的容器已经运行起来。此时访问 http://{ip}:3000 应该就能访问到该vue应用:
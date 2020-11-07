---
title: Windows平台安装MongoDB教程
date: 2020-08-04 11:03:09
tags:
categories:
---

## 一、MongoDB简介

MongoDB是一个基于分布式文件存储的数据库，由C++语言编写，旨在为WEB应用提供可扩展的高性能数据存储解决方案。

MongoDB将数据存储为一个文档，数据结构由键值(key=>value)对组成，MongoDB文档类似于JSON对象，字段值可以包含其他文档，数组及文档数组。

MongoDB服务端可运行在Linux、Windows或mac os x平台，支持32位和64位应用，默认端口为27017。

MongoDB支持各种编程语言: Python，Java，C++，PHP，C#等多种语言。

<!-- more -->

## 二、下载MongoDB

MongoDB提供了可用于32位系统和64位系统的预编译二进制包（新版本没有了32位系统的安装文件），你可以进入MongoDB官网下载安装，MongoDB的预编译二进制包的下载地址为：https://www.mongodb.com/download-center/community，打开之后会看到如下图，直接点击Download下载即可，也可以在Version中选择你想要的版本：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804110433.png)


## 三、安装MongoDB
双击打开文件进行安装，在安装过程中，可以通过点击 "Custom(自定义)" 按钮来设置你的安装目录。

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804110505.png)

这里我选择安装在E:\MongoDB这个目录下（安装目录会影响我们后面的配置）。

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804110526.png)

这里选择直接next：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804110543.png)

这里安装 "Install MongoDB Compass" 不勾选，否则可能要很长时间都一直在执行安装，MongoDB Compass是一个图形界面管理工具，这里不安装也是没有问题的，可以自己去下载一个图形界面管理工具，比如Robo3T。

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804110606.png)

之后稍微等待一会就安装好了。

## 四、配置MongoDB

首先要在MongoDB的data文件夹里新建一个db文件夹和一个log文件夹：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804110642.png)

然后在log文件夹下新建一个mongo.log：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804110702.png)

然后将E:\MongoDB\bin添加到环境变量path中，此时打开cmd窗口运行一下mongo命令，出现如下情况：

这是为什么呢？这是因为我们还没有启动MongoDB服务，自然也就连接不上服务了。那要怎么启动呢？在cmd窗口中运行如下命令：

```
 mongod --dbpath E:\MongoDB\data\db

```

需要注意的是：如果你没有提前创建db文件夹，是无法启动成功的。运行成功之后，我们打开浏览器，输入127.0.0.1:27017，看到如下图，就说明MongoDB服务已经成功启动了。

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804110754.png)

但是如果每次都要这么启动服务的话也太麻烦了吧，这里你可以选择设置成开机自启动，也可以选择用命令`net start mongodb`来手动启动，这里我选择使用后者，具体方法如下。

还是打开cmd窗口，不过这次是以管理员身份运行，然后输入如下命令：

```
mongod --dbpath "E:\MongoDB\data\db" --logpath "E:\MongoDB\data\log\mongo.log" -install -serviceName "MongoDB"

```

如果没有报错的话就说明成功添加到服务里了，可以使用win+R然后输入services.msc命令进行查看：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804110837.png)

默认是自动运行的，这里我选择把它改成手动的(随自己情况)。然后在cmd窗口中运行net start mongodb：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804110915.png)

怎么解决呢？两个步骤：

1）运行sc delete mongodb删除服务；

2）再运行一次配置服务的命令：
```
mongod --dbpath "E:\MongoDB\data\db" --logpath "E:\MongoDB\data\log\mongo.log" -install -serviceName "MongoDB"

```

然后再运行net start mongodb，服务启动成功：

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200804111051.png)

##  五、可能遇到的问题
**1.mongod不是内部或外部命令**
出现这种问题说明你没有把bin目录添加到环境变量之中，重新添加一下即可解决。

**2.服务名无效**
首先是看你输入的服务名称是否有误，然后再查看本地服务中有没有MongoDB服务，如果没有服务，则运行命令添加服务即可。

**3.发生服务特定错误：100**
删除db文件夹下的mongod.lock和storage.bson两个文件，若删除完之后仍然出现这种问题，用sc delete mongodb删除服务，再配置一下服务就能解决了。


## 安装参考二
https://www.runoob.com/mongodb/mongodb-window-install.html









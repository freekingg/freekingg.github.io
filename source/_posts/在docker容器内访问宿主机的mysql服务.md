---
title: 在docker容器内访问宿主机的mysql服务
date: 2020-08-11 20:55:16
tags:
categories:
---

## 背景
宿主机部署MySQL、Redis，docker内部署node项目

## 需求：
docker内的node应用访问宿主机的MySQL和Redis

## 方法：

一、连接地址切记不能用localhost和127.0.0.1

这些地址代表的都是容器内的系统，根本没有访问到宿主机，会一直报连接mysql/redis异常。

二、用docker的虚拟网卡地址

在宿主机查询网卡情况------ifconfig

docker0这块虚拟网卡的 inet 地址就是正确的本地ip(如172.17.0.1)

使用ifconfig命令可以看到，有一个docker0和eth0，在docker容器中可以通过eth0的IP地址加上端口号（3306）这样就可以连接上宿主机的MySQL了；另外，nginx可以通过docker0的IP地址加上构建容器时指定的端口号进行访问容器。

![](https://raw.githubusercontent.com/freekingg/king-static/master/20200811205741.png)

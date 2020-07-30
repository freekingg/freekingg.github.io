---
title: linux安装java-jdk与jenkins
date: 2020-07-30 20:07:37
tags:
categories:
---


## 安装jdk8 

先查看系统是否已有自带的jdk

```
rpm -qa |grep java

rpm -qa |grep jdk

rpm -qa |grep gcj
```

如果没有输出信息，则说明系统没有安装。如果有输出信息，则执行下面的命令卸载

```
rpm -qa | grep java | xargs rpm -e --nodeps

```

列出所有可安装的rpm软件包

```
yum list java-1.8*

```
此时会输出一大堆软件包列表。

安装
```
yum install java-1.8.0-openjdk* -y

```

验证是否安装成功
```
java -version
```
到此安装成功。可见yum已经自动为我们配置好了java环境变量，很方便吧。

## 安装jenkins

[jenkins](https://pkg.jenkins.io/redhat-stable/)
[参考2](https://www.jianshu.com/p/180fb11a5b96)
```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins.io/redhat/jenkins.repo
sudo rpm --import http://pkg.jenkins.io/redhat/jenkins.io.key
sudo yum install jenkins
```

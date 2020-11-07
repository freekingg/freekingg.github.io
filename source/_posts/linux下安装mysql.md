---
title: linux下安装mysql及问题
date: 2020-08-11 14:24:23
tags:
categories:
---
## 下载安装包

```
wget http://repo.mysql.com/mysql57-community-release-el7-8.noarch.rpm
```

未安装wget的同学执行以下命令安装

```
sudo yum install wget
```


<!-- more -->

## 安装
```
 sudo rpm -ivh mysql57-community-release-el7-8.noarch.rpm
```

```
sudo yum install mysql-server
```

安装完成

## 启动
```
systemctl start mysqld
```


## 设置密码
当第一次启动MySQL服务器时，为MySQL根用户生成一个临时密码。 您可以通过运行以下文件找到密码：
```
/var/log/mysqld.log
```
找到里面的 `localhost：`后边的就是临时密码，先复制下来

tip:

如果这个文件为空：
- 1.删除原来安装过的mysql残留的数据
```
rm -rf /var/lib/mysql
```
- 2.重启mysqld服务
```
systemctl restart mysqld
```
- 3.再去找临时密码

## 配置mysql安装项：
```
sudo mysql_secure_installation
```
输入刚才的临时密码

接下来mysql会提示你输入新的密码,
和一下其他设置，一般情况下我们都会输入y（密码必须至少包含8个字符并且至少包含一个大写字母，一个小写字母，一个数字和一个特殊字符。）

## 完成
看到All done!表示配置已经完成，尝试远程访问一下


## 远程连接

访问成功，但是在利用SQLyog工具连接出现了问题


报错:不允许远程登陆
```
ERROR 1130: Host ’...′ is not allowed to connect to this MySQL server
```
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200811150445.png)


- 1、连接服务器: mysql -u root -p

- 2、看当前所有数据库：show databases;

- 3、进入mysql数据库：use mysql;

- 4、查看mysql数据库中所有的表：show tables;

- 5、修改user表中的```update user set host = '%' where user = 'root';```

- 6、最后刷新一下：flush privileges;

**注意**

将Host设置为通配符%

Host列指定了允许用户登录所使用的IP，比如user=root Host=192.168.1.1。这里的意思就是说root用户只能通过192.168.1.1的客户端去访问。 user=root Host=localhost，表示只能通过本机客户端去访问。而%是个通配符，如果Host=192.168.1.%，那么就表示只要是IP地址前缀为“192.168.1.”的客户端都可以连接。如果Host=%，表示所有IP都有连接权限。 

注意：在生产环境下不能为了省事将host设置为%，这样做会存在安全问题，具体的设置可以根据生产环境的IP进行设置；



## 开机自启动
```
systemctl enable mysqld
```
---
title: linux安装jenkins
date: 2020-07-30 20:07:37
tags:
- 安装
- 部署
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
http://mirror.esuni.jp/jenkins/updates/update-center.json
<!-- more -->

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

[jenkins](https://www.jenkins.io/doc/book/installing/#red-hat-centos)
[参考2](https://www.jianshu.com/p/180fb11a5b96)
```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins.io/redhat/jenkins.repo
sudo rpm --import http://pkg.jenkins.io/redhat/jenkins.io.key
sudo yum install jenkins

```

**配置权限**
为了不因为权限出现各种问题，这里直接使用root

修改用户为root
```
vim /etc/sysconfig/jenkins

#修改配置
$JENKINS_USER="root"
```
修改目录权限
```
chown -R root:root /var/lib/jenkins
chown -R root:root /var/cache/jenkins
chown -R root:root /var/log/jenkins
```
重启
```
service jenkins restart
ps -ef | grep jenkins
```


使用以下命令启动Jenkins服务：
```
sudo systemctl start jenkins
```

使用以下命令检查Jenkins服务的状态：
```
sudo systemctl status jenkins
```


## Jenkins 使用国内镜像快速安装（rpm安装）
### 下载rpm包
是目前最新 jenkins LTS版本: 可以在去 清华大学镜像站 看一下，替换成最新的链接
```
wget https://mirrors.tuna.tsinghua.edu.cn/jenkins/redhat-stable/jenkins-2.204.3-1.1.noarch.rpm
```

### 安装并启动
```
sudo yum install jenkins-2.204.3-1.1.noarch.rpm
```

### 修改端口
jenkins 默认8080端口，如果和别的程序冲突，可以修改端口号
```
vim /etc/sysconfig/jenkins
```

### 开启服务
```
systemctl start jenkins.service
```

### 获取访问秘钥，复制下来，接下来要验证
```
cat /var/lib/jenkins/secrets/initialAdminPassword
```

### 修改默认镜像源
```
vim /var/lib/jenkins/hudson.model.UpdateCenter.xml
```

将 url 修改为 清华大学官方镜像：https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
```
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json</url>
  </site>
</sites>
```
### 访问
```
http://ip地址:端口号
```

## Docker 快速安装Jenkins实用教程

### 查找镜像
```
docker search jenkins
```
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731110630.png)

### 下载Jenkins镜像文件
```
docker pull jenkins/jenkins
```
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731110705.png)


### 创建Jenkins挂载目录并授权
我们在服务器上先创建一个jenkins工作目录 /var/jenkins_node，赋予相应权限，稍后我们将jenkins容器目录挂载到这个目录上，这样我们就可以很方便地对容器内的配置文件进行修改。

```
mkdir -p /var/jenkins_node
chmod 777 /var/jenkins_node
```

### 创建并启动容器
```
docker run -d -p 10240:8080 -p 10241:50000 -v /var/jenkins_node:/var/jenkins_home -v /etc/localtime:/etc/localtime --name myjenkins jenkins/jenkins
```
- `-d` 后台运行镜像

- `-p 10240:8080` 将镜像的8080端口映射到服务器的10240端口。

- `-p 10241:50000` 将镜像的50000端口映射到服务器的10241端口

- `-v /var/jenkins_node:/var/jenkins_home` /var/jenkins_home目录为容器jenkins工作目录，我们将硬盘上的一个目录挂载到这个位置，方便后续更新镜像后继续使用原来的工作目录。这里我们设置的就是上面我们创建的 /var/jenkins_node目录

- `-v /etc/localtime:/etc/localtime` 让容器使用和服务器同样的时间设置。

- `--name myjenkins` 给容器起一个别名


### 查看容器状态
```
docker ps
```
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731111050.png)

### 查看容器日志
```
docker logs myjenkins
```
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731111130.png)

### 配置镜像加速
可供选择的jenkins2 插件镜像列表：
Jenkins 所有镜像列表： http://mirrors.jenkins-ci.org/status.html
```
比如日本的镜像：
http://mirror.esuni.jp/jenkins/,
http://ftp.yz.yamagata-u.ac.jp/pub/misc/jenkins/，
http://ftp.tsukuba.wide.ad.jp/software/jenkins/
德国的镜像：
http://jenkins.mirror.isppower.de/ ，
http://mirror.yandex.ru/mirrors/jenkins/
官方的镜像：
http://mirrors.jenkins-ci.org/， 
http://archives.jenkins-ci.org/
```

推荐清华大学镜像：
https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json


```
cd /var/jenkins_node
```
修改 `hudson.model.UpdateCenter.xml`里的内容
```
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>https://updates.jenkins.io/update-center.json</url>
  </site>
</sites>
```
将 url 修改为 清华大学官方镜像：https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
```
<?xml version='1.1' encoding='UTF-8'?>
<sites>
  <site>
    <id>default</id>
    <url>https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json</url>
  </site>
</sites>
```
![](https://raw.githubusercontent.com/freekingg/king-static/master/20200731111426.png)


### 访问
你的ip地址加上端口号
http://ip地址:1024

### 写在最后
如果我们不配置镜像加速，那么很有可能会出现： Jenkins 一直卡在 启动界面 Please wait while Jenkins is getting ready to work …









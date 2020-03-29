---
title: npm入门使用指南
date: 2020-03-29 17:26:26
tags:
- nodejs
- npm
- 知识点
categories:
- nodejs
---

NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题，常见的使用场景有以下几种：

- 允许用户从NPM服务器下载别人编写的第三方包到本地使用。
- 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
- 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。

由于新版的nodejs已经集成了npm，所以之前npm也一并安装好了。同样可以通过输入 "npm -v" 来测试是否成功安装。命令如下，出现版本提示表示安装成功:

``` javascript
npm -v
5.5.1
```
<!-- more -->
如果你安装的是旧版本的 npm，可以很容易得通过 npm 命令来升级，命令如下：

如果是 Window 系统使用以下命令即可：
``` javascript
npm install npm -g
```

### **使用 npm 命令安装模块**
npm 安装 Node.js 模块语法格式如下：

``` javascript
npm install <Module Name>
```

以下实例，我们使用 npm 命令安装常用的 Node.js web框架模块 express:

``` javascript
npm install express
```

安装好之后，express 包就放在了工程目录下的 node_modules 目录中，因此在代码中只需要通过 require('express') 的方式就好，无需指定第三方包路径。

``` javascript
var express = require('express');
```
### **全局安装与本地安装**
npm 的包安装分为本地安装（local）、全局安装（global）两种，从敲的命令行来看，差别只是有没有-g而已
比如:
``` javascript
npm install express      # 本地安装
npm install express -g   # 全局安装
```

### **本地安装**
1. 将安装包放在 ./node_modules 下（运行 npm 命令时所在的目录），如果没有 node_modules 目录，会在当前执行 npm 命令的目录下生成 node_modules 目录。
2. 可以通过 require() 来引入本地安装的包。

### **全局安装**
1. 将安装包放在 /usr/local 下或者你 node 的安装目录。
2. 可以直接在命令行里使用。

### **查看安装信息**
你可以使用以下命令来查看所有全局安装的模块：

``` javascript
$ npm list -g

├─┬ cnpm@4.3.2
│ ├── auto-correct@1.0.0
│ ├── bagpipe@0.3.5
│ ├── colors@1.1.2
│ ├─┬ commander@2.9.0
│ │ └── graceful-readlink@1.0.1
│ ├─┬ cross-spawn@0.2.9
│ │ └── lru-cache@2.7.3
……
```
### **使用 package.json**
每个项目的根目录下面，一般都有一个package.json文件，定义了这个项目所需要的各种模块，以及项目的配置信息（比如名称、版本、许可证等元数据）。npm install命令根据这个配置文件，自动下载所需的模块，也就是配置项目所需的运行和开发环境。
直接的说：就是管理你本地安装的npm包 
``` javascript
{
	"name": "Hello World",
	"version": "0.0.1",
	"author": "张三",
	"description": "第一个node.js程序",
	"keywords":["node.js","javascript"],
	"repository": {
		"type": "git",
		"url": "https://path/to/url"
	},
	"license":"MIT",
	"engines": {"node": "0.10.x"},
	"bugs":{"url":"http://path/to/bug","email":"bug@example.com"},
	"contributors":[{"name":"李四","email":"lisi@example.com"}],
	"scripts": {
		"start": "node index.js"
	},
	"dependencies": {
		"express": "latest",
		"mongoose": "~3.8.3",
		"handlebars-runtime": "~1.0.12",
		"express3-handlebars": "~0.5.0",
		"MD5": "~1.2.0"
	},
	"devDependencies": {
		"bower": "~1.2.8",
		"grunt": "~0.4.1",
		"grunt-contrib-concat": "~0.3.0",
		"grunt-contrib-jshint": "~0.7.2",
		"grunt-contrib-uglify": "~0.2.7",
		"grunt-contrib-clean": "~0.5.0",
		"browserify": "2.36.1",
		"grunt-browserify": "~1.3.0",
	}
}
```
### **Package.json 属性说明**

- name - 包名。

- version - 包的版本号。

- description - 包的描述。

- homepage - 包的官网 url 。

- author - 包的作者姓名。

- contributors - 包的其他贡献者姓名。

- dependencies - 依赖包列表。如果依赖包没有安装，npm 会自动将依赖包安装在 node_module 目录下。

- repository - 包代码存放的地方的类型，可以是 git 或 svn，git 可在 Github 上。

- main - main 字段指定了程序的主入口文件，require('moduleName') 就会加载这个文件。这个字段的默认值是模块根目录下面的 index.js。

- keywords - 关键字

**详细介绍参考** http://javascript.ruanyifeng.com/nodejs/packagejson.html

### **卸载模块**
我们可以使用以下命令来卸载 Node.js 模块。

``` javascript
npm uninstall express

```
### **更新模块**
我们可以使用以下命令更新模块：
``` javascript
npm update express
```
### **搜索模块**
使用以下来搜索模块：
``` javascript
npm search express
```
### **创建模块**
创建模块，package.json 文件是必不可少的。我们可以使用 NPM 生成 package.json 文件，生成的文件包含了基本的结果。

``` javascript
npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg> --save` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
name: (node_modules) test                   # 模块名
version: (1.0.0) 
description: Node.js 测试模块(www.test.com)  # 描述
entry point: (index.js) 
test command: make test
git repository: https://github.com/test/runoob.git  # Github 地址
keywords: 
author: 
license: (ISC) 
About to write to ……/node_modules/package.json:      # 生成地址

{
  "name": "learn",
  "version": "1.0.0",
  "description": "Node.js 测试模块",
  ……
}


Is this ok? (yes) yes
```
以上的信息，你需要根据你自己的情况输入。在最后输入 "yes" 后会生成 package.json 文件。

### **常用命令**
``` javascript
npm install [-g] 本地或全局安装模块
npm uninstall [-g] 本地或全局卸载模块
npm update 更新模块
npm ls 查看安装的模块
npm list 列出已安装模块
npm show  显示模块详情
npm info 查看模块的详细信息
npm search 搜索模块
npm publish 发布模块
npm unpublish 删除已发布的模块
npm -v 或 npm version显示版本信息
npm view npm versions 列出npm 的所有有效版本
npm install -g npm@2.14.14 /npm update -g npm@2.14.14  安装指定的npm版本
npm init 引导创建一个package.json文件，包括名称、版本、作者这些信息等
npm outdated  #检查模块是否已经过时
npm root  [-g] 查看包的安装路径，输出 node_modules的路径，
npm help 查看某条命令的详细帮助 例如输入npm help install
npm config 管理npm的配置路径
```

### **使用淘宝 NPM 镜像**
大家都知道国内直接使用 npm 的官方镜像是非常慢的，这里推荐使用淘宝 NPM 镜像。

淘宝 NPM 镜像是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步。

你可以使用淘宝定制的 cnpm (gzip 压缩支持) 命令行工具代替默认的 npm:
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
``` javascript
这样就可以使用 cnpm 命令来安装模块了：
```
 cnpm install [name]
```

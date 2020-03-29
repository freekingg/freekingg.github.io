---
title: nodeJs入门系列
date: 2020-03-29 17:12:23
tags:
- nodeJs入门系列
- nodejs
categories:
- nodejs
---

## 01 介绍/安装/

### **node.js 介绍**
Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。
Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。
Node.js 的包管理器 npm，是全球最大的开源库生态系统。

<!-- more -->

### **Node.js优点**
1、采用事件驱动、异步编程，为网络服务而设计。其实Javascript的匿名函数和闭包特性非常适合事件驱动、异步编程。而且JavaScript也简单易学，很多前端设计人员可以很快上手做后端设计。
2、Node.js非阻塞模式的IO处理给Node.js带来在相对低系统资源耗用下的高性能与出众的负载能力，非常适合用作依赖其它IO资源的中间层服务。
3、Node.js轻量高效，可以认为是数据密集型分布式部署环境下的实时应用系统的完美解决方案。

Node非常适合如下情况：
在响应客户端之前，您预计可能有很高的流量，但所需的服务器端逻辑和处理不一定很多。

### **Node.js缺点**
1、可靠性低
2、单进程，单线程，只支持单核CPU，不能充分的利用多核CPU服务器。一旦这个进程崩掉，那么整个web服务就崩掉了。不过以上缺点可以可以通过代码的健壮性来弥补。

### **Node.js 安装**
中文官网地址：
http://nodejs.cn/download/，进入下载页面选择对应的版本安装即可。

更详细的安装教程，包含各类操作系统：
http://www.runoob.com/nodejs/nodejs-install-setup.html

### **检查是否安装成功**
命令行输入：
```
node -v
```
返回node.js 版本号即代表安装成功。

### **在命令行中实现`hello world`**
``` javascript
C:\Users\Administrator>node \\此处是一个回车,既进入node交互环境
> console.log('hello world');
输出：hello world
```
### **创建一个js文件并执行它(t.js)**
``` javascript
//t.js

console.log('hello world');
```
执行：
``` javascript
D:\>cd node.js
D:\node.js>node t.js
输出：hello world
```

### 安装nodemon监控代码调试
在开发nodejs程序，调试的时候，无论你修改了代码的哪一部分，都需要重启服务才能生效。这是因为 Node.js 只有在第一次引用到某部份时才会去解析脚本文件，以后都会直接访问内存，避免重复载入。。Node.js的这种设计虽然有利于提高性能，却不利于开发调试，因为我们在开发过程中总是希望修改后立即看到效果，而不是每次都要终止进程并重启。nodemon 可以帮助你实现这个功能，它会监视你对代码的改动，并自动重启 Node.js。

nodemon的安装也很简单：

直接用npm安装既可，键入命令: 

``` javascript
 npm -g install nodemon
```

nodemon 来启动服务了。

``` javascript
nodemon index.js
```

现在，代码发生改变，nodemon会自动重启 Node.js；

### **服务端原理、语言倾诉对象**
使用node.js作为后端的项目，客户访问过程：
客户端发起请求 -> node.js 服务器接到请求并运算、解析 -> 返回请求结果。
我们所编写代码语言的倾诉对象就是 node.js 服务器端。

## 02 NodeJs 创建第一个应用
在我们创建 Node.js 第一个 "Hello, World!" 应用前，让我们先了解下 Node.js 应用是由哪几部分组成的：

 1. 引入 required 模块：我们可以使用 require 指令来载入 Node.js 模块。
 2. 创建服务器：服务器可以监听客户端的请求，类似于 Apache 、Nginx 等 HTTP 服务器。
 3. 接收请求与响应请求 服务器很容易创建，客户端可以使用浏览器或终端发送 HTTP 请求，服务器接收请求后返回响应数据。

### 创建 Node.js 应用
**步骤一、引入 required 模块**
我们使用 require 指令来载入 http 模块，并将实例化的 HTTP 赋值给变量 http，实例如下:
``` javascript
var http = require("http"); //使用 require函数获取 http模块
```
**步骤二、创建服务器**
接下来我们使用 http.createServer() 方法创建服务器，并使用 listen 方法绑定 8888 端口。 函数通过 request, response 参数来接收和响应数据。

实例如下，在你项目的根目录下创建一个叫 server.js 的文件，并写入以下代码：
``` javascript
//使用 require函数获取 http模块
var http = require('http');

http.createServer(function (request, response) {
    //不处理favicon.ico
    if(request.url == "/favicon.ico"){return;}
    // 发送 HTTP 头部 
    // HTTP 状态值: 200 : OK
    // 内容类型: text/html,解决中文乱码问题
    response.writeHead(200, {'Content-Type': 'text/html;  charset=utf-8'});
    //设置编码类型
    response.write('<head><meta charset="utf-8"/></head>');  
    // 发送响应数据 "Hello World"
    response.write("中文");
    response.end('Hello World');
}).listen(8888);
console.log('Server running at http://127.0.0.1:8888/');
```
以上代码我们完成了一个可以工作的 HTTP 服务器。
使用 node 命令执行以上的代码：
``` javascript
node server.js
Server running at http://127.0.0.1:8888/
```

接下来，打开浏览器访问 http://127.0.0.1:8888/，你会看到一个写着 "中文 Hello World"的网页。
 

## 03 NPM 使用介绍
NPM是随同NodeJS一起安装的包管理工具，能解决NodeJS代码部署上的很多问题，常见的使用场景有以下几种：

- 允许用户从NPM服务器下载别人编写的第三方包到本地使用。
- 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
- 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用。

由于新版的nodejs已经集成了npm，所以之前npm也一并安装好了。同样可以通过输入 "npm -v" 来测试是否成功安装。命令如下，出现版本提示表示安装成功:

``` javascript
npm -v
5.5.1
```

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
``` javascript
npm install -g cnpm --registry=https://registry.npm.taobao.org
``` 
这样就可以使用 cnpm 命令来安装模块了：
``` javascript
 cnpm install [name]
```

## 04 模块（module）

Node.js中，一个JavaScript文件中定义的变量、函数，都只在这个文件内部有效。当需要从模块外部引用这些变量、函数时，必须使用exports对象进行暴露。使用者要用require()命令引用这个JS文件。

foo.js文件中的代码：
``` javascript
var msg = "你好";

exports.msg = msg; 
//msg这个变量，是一个js文件内部才有作用域的变量。如果别人想用这个变量，那么就要用exports进行暴露。
```
使用者：
``` javascript
var foo = require("./test/foo.js");
//使用者用foo来接收exports对象，也就是说，这里的foo变量，就是文件中的exports变量。

console.log(foo.msg);
```
一个JavaScript文件，可以向外exports无数个变量、函数。但是require的时候，仅仅需要require这个JS文件一次。使用的它的变量、函数的时候，用点语法即可

js文件中，可以用exports暴露很多东西，比如函数、变量。
``` javascript
var msg = "你好";
var info = "呵呵";

function showInfo(){
    console.log(info);
}

exports.msg = msg;
exports.info = info;
exports.showInfo = showInfo;
``` 
在使用者中，只需要require一次。
``` javascript
var foo = require("./test/foo.js");
```
相当于增加了顶层变量。所有的函数、变量都要从这个顶层变量走：
``` javascript
console.log(foo.msg);
console.log(foo.info);
foo.showInfo();
```
Node中，js文件和js文件，就是被一个个exports和require构建成为网状的。

如果在require命令中，这么写:`var foo = require("foo.js");` 没有写`./`,所以不是一个相对路径。是一个特殊的路径,那么Node.js将该文件视为`node_modules`目录下的一个文件

### **概述**
Node中模块分为两类：一类是Node提供的模块，称为核心模块；另一类是用户编写的模块，称为文件模块

核心模块部分在Node源代码的编译过程中，编译进了二进制执行文件。在Node进程启动时，部分核心模块就被直接加载进内存中，所以这部分核心模块引入时，文件定位和编译执行这两个步骤可以省略掉，并且在路径分析中优先判断，所以它的加载速度是最快的

文件模块则是在运行时动态加载，需要完整的路径分析、文件定位、编译执行过程，速度比核心模块慢

### **module对象信息**
``` javascript
module.id 模块的识别符，通常是带有绝对路径的模块文件名。
module.filename 模块的文件名，带有绝对路径。
module.loaded 返回一个布尔值，表示模块是否已经完成加载。
module.parent 返回一个对象，表示调用该模块的模块。
module.children 返回一个数组，表示该模块要用到的其他模块。
module.exports 表示模块对外输出的值。
```
![image.png-42.3kB][1]

[1]: http://static.zybuluo.com/wp0214/z19k9k3jwvp9h0oop300x1lh/image.png

## 05 文件系统操作-fs

>**fs文件系统用于对系统文件及目录进行读写操作**

### 文件读取

#### 普通读取

同步读取

```javascript
var fs = require('fs');
var data;

try{
    data = fs.readFileSync('./fileForRead.txt', 'utf8');
    console.log('文件内容: ' + data);
}catch(err){
    console.error('读取文件出错: ' + err.message);
}
```

输出如下：

```powershell
/usr/local/bin/node readFileSync.js
文件内容: hello world
```

异步读取

```javascript
var fs = require('fs');

fs.readFile('./fileForRead.txt', 'utf8', function(err, data){
    if(err){
        return console.error('读取文件出错: ' + err.message);
    }
    console.log('文件内容: ' + data);
});
```

输出如下

```powershell
/usr/local/bin/node readFile.js
文件内容: hello world
```

#### 通过文件流读取

适合读取大文件

```javascript
var fs = require('fs');
var readStream = fs.createReadStream('./fileForRead.txt', 'utf8');

readStream
    .on('data', function(chunk) {
        console.log('读取数据: ' + chunk);
    })
    .on('error', function(err){
        console.log('出错: ' + err.message);
    })
    .on('end', function(){  // 没有数据了
        console.log('没有数据了');
    })
    .on('close', function(){  // 已经关闭，不会再有事件抛出
        console.log('已经关闭');
    });
```

输出如下

```powershell
/usr/local/bin/node createReadStream.js
读取数据: hello world
没有数据了
已经关闭
``` 

### 文件写入

备注：以下代码，如果文件不存在，则创建文件；如果文件存在，则覆盖文件内容；

异步写入

```javascript
var fs = require('fs');

fs.writeFile('./fileForWrite.txt', 'hello world', 'utf8', function(err){
    if(err) throw err;
    console.log('文件写入成功');
});
```

同步写入

```javascript
var fs = require('fs');

try{
    fs.writeFileSync('./fileForWrite1.txt', 'hello world', 'utf8');
    console.log('文件写入成功');
}catch(err){
    throw err;
}
```

#### 通过文件流写入

```javascript
var fs = require('fs');
var writeStream = fs.createWriteStream('./fileForWrite1.txt', 'utf8');

writeStream
    .on('close', function(){  // 已经关闭，不会再有事件抛出
        console.log('已经关闭');
    });

writeStream.write('hello');
writeStream.write('world');
writeStream.end('');
```

#### 相对底层的接口

>fs.write(fd, buffer, offset, length[, position], callback)
>fs.write(fd, data[, position[, encoding]], callback)
>fs.writeSync(fd, buffer, offset, length[, position])
>fs.writeSync(fd, data[, position[, encoding]])

* fd：写入的文件句柄。
* buffer：写入的内容。
* offset：将buffer从offset位置开始，长度为length的内容写入。
* length：写入的buffer内容的长度。
* position：从打开文件的position处写入。
* callback：参数为 `(err, written, buffer)`。`written`表示有xx字节的buffer被写入。

备注：`fs.write(fd, buffer, offset, length[, position], callback)`跟`fs.write(fd, data[, position[, encoding]], callback)`的区别在于：后面的只能把所有的data写入，而前面的可以写入指定的data子串？

### 文件是否存在

`fs.exists()`已经是`deprecated`状态，现在可以通过下面代码判断文件是否存在。

```javascript
var fs = require('fs');

fs.access('./fileForRead.txt', function(err){
    if(err) throw err;
    console.log('fileForRead.txt存在');
});

fs.access('./fileForRead2.txt', function(err){
    if(err) throw err;
    console.log('fileForRead2.txt存在');
});
```

`fs.access()`除了判断文件是否存在（默认模式），还可以用来判断文件的权限。

备忘：`fs.constants.F_OK`等常量无法获取（node v6.1，mac 10.11.4下，`fs.constants`是`undefined`）

### 创建目录

异步版本（如果目录已存在，会报错）

```javascript
var fs = require('fs');

fs.mkdir('./hello', function(err){
    if(err) throw err;
    console.log('目录创建成功');
});
```

同步版本

```javascript
var fs = require('fs');

fs.mkdirSync('./hello');
```

### 删除文件

```javascript
var fs = require('fs');

fs.unlink('./fileForUnlink.txt', function(err){
    if(err) throw err;
    console.log('文件删除成功');
});
```

```javascript
var fs = require('fs');

fs.unlinkSync('./fileForUnlink.txt');
```

### 创建目录

```javascript
// fs.mkdir(path[, mode], callback)
var fs = require('fs');

fs.mkdir('sub', function(err){
    if(err) throw err;
    console.log('创建目录成功');
});
```

```javascript
// fs.mkdirSync(path[, mode])
var fs = require('fs');

try{
    fs.mkdirSync('hello');
    console.log('创建目录成功');
}catch(e){
    throw e;
}
```

### 遍历目录

同步版本，注意：`fs.readdirSync()`只会读一层，所以需要判断文件类型是否目录，如果是，则进行递归遍历。

```javascript
// fs.readdirSync(path[, options])

var fs = require('fs');
var path = require('path');

var getFilesInDir = function(dir){

    var results = [ path.resolve(dir) ];
    var files = fs.readdirSync(dir, 'utf8');

    files.forEach(function(file){

        file = path.resolve(dir, file);

        var stats = fs.statSync(file);

        if(stats.isFile()){
            results.push(file);
        }else if(stats.isDirectory()){
            results = results.concat( getFilesInDir(file) );
        }
    });

    return results;
};

var files = getFilesInDir('../');
console.log(files);
```

异步版本：（TODO）

```javascript

```

### 文件重命名

```javascript
// fs.rename(oldPath, newPath, callback)
var fs = require('fs');

fs.rename('./hello', './world', function(err){
    if(err) throw err;
    console.log('重命名成功');
});
```

```javascript
fs.renameSync(oldPath, newPath)
var fs = require('fs');

fs.renameSync('./world', './hello');
```
### 移动文件
移动文件也是我们经常会遇见的，可是fs没有专门移动文件的函数，但是我们可以通过rename函数来达到移动文件的目的，示例如下。
```
var fs = require('fs');

fs.rename(oldPath,newPath,function (err) {
   if (err) throw err;
   console.log('renamed complete');
});
```

### 监听文件修改

`fs.watch()`比`fs.watchFile()`高效很多（why）

#### fs.watchFile()

实现原理：轮询。每隔一段时间检查文件是否发生变化。所以在不同平台上表现基本是一致的。

```javascript
var fs = require('fs');

var options = {
    persistent: true,  // 默认就是true
    interval: 2000  // 多久检查一次
};

// curr, prev 是被监听文件的状态, fs.Stat实例
// 可以通过 fs.unwatch() 移除监听
fs.watchFile('./fileForWatch.txt', options, function(curr, prev){
    console.log('修改时间为: ' + curr.mtime);
});
```

修改`fileForWatch.txt`，可以看到控制台下打印出日志

```powershell
/usr/local/bin/node watchFile.js
修改时间为: Sat Jul 16 2016 19:03:57 GMT+0800 (CST)
修改时间为: Sat Jul 16 2016 19:04:05 GMT+0800 (CST)
```

为啥子？莫非单纯访问文件也会触发回调？

>If you want to be notified when the file was modified, not just accessed, you need to compare curr.mtime and prev.mtime.

在 **v0.10** 之后的改动。如果监听的文件不存在，会怎么处理。如下

>Note: when an fs.watchFile operation results in an ENOENT error, it will invoke the listener once, with all the fields zeroed (or, for dates, the Unix Epoch). In Windows, blksize and blocks fields will be undefined, instead of zero. If the file is created later on, the listener will be called again, with the latest stat objects. This is a change in functionality since v0.10.

#### fs.watch()

>fs.watch(filename[, options][, listener])
>fs.unwatchFile(filename[, listener])

这接口非常不靠谱（当前测试用的v6.1.0），参考 https://github.com/nodejs/node/issues/7420

>fs.watch(filename[, options][, listener])#

注意：`fs.watch()`这个接口并不是在所有的平台行为都一致，并且在某些情况下是不可用的。`recursive`这个选项只在`mac`、`windows`下可用。

问题来了：

1. 不一致的表现。
2. 不可用的场景。
3. linux上要recursive咋整。

>The fs.watch API is not 100% consistent across platforms, and is unavailable in some situations.
>The recursive option is only supported on OS X and Windows.

备忘，不可用的场景。比如网络文件系统等。

>For example, watching files or directories can be unreliable, and in some cases impossible, on network file systems (NFS, SMB, etc), or host file systems when using virtualization software such as Vagrant, Docker, etc.

另外，listener回调有两个参数，分别是`event`、`filename`。其中，`filename`仅在linux、windows上会提供，并且不是100%提供，所以，尽量不要依赖`filename`。

在linux、osx上，`fs.watch()`监听的是inode。如果文件被删除，并重新创建，那么删除事件会触发。同时，`fs.watch()`监听的还是最初的inode。（API的设计就是这样的）

结论：怎么看都感觉这个API很不靠谱，虽然性能比fs.watchFile()要高很多。

先来个例子，在osx下测试了一下，简直令人绝望。。。无论是创建、修改、删除文件，`evt`都是`rename`。。。

```javascript
var fs = require('fs');

var options = {
    persistent: true,
    recursive: true,
    encoding: 'utf8'
};

fs.watch('../', options, function(event, filename){
    console.log('触发事件:' + event);
    if(filename){
        console.log('文件名是: ' + filename);
    }else{
        console.log('文件名是没有提供');
    }
});
```

修改下`fileForWatch.txt`，看到下面输出。。。感觉打死也不想用这个API。。。

贴下环境：osx 10.11.4, node v6.1.0。

```powershell
触发事件:rename
文件名是: fs/fileForWatch.txt___jb_bak___
触发事件:rename
文件名是: fs/fileForWatch.txt
触发事件:rename
文件名是: fs/fileForWatch.txt___jb_old___
触发事件:rename
文件名是: .idea/workspace.xml___jb_bak___
触发事件:rename
文件名是: .idea/workspace.xml
触发事件:rename
文件名是: .idea/workspace.xml___jb_old___
```

### 修改所有者

参考linux命令行，不举例子了。。。

>fs.chown(path, uid, gid, callback)
>fs.chownSync(path, uid, gid)
>fs.fchown(fd, uid, gid, callback)
>fs.fchownSync(fd, uid, gid)

### 修改权限

可以用`fs.chmod()`，也可以用`fs.fchmod()`。两者的区别在于，前面传的是文件路径，后面传的的文件句柄。

1. `fs.chmod)`、`fs.fchmod()`区别：传的是文件路径，还是文件句柄。
2. `fs.chmod()`、`fs.lchmod()`区别：如果文件是软连接，那么`fs.chmod()`修改的是软连接指向的目标文件；`fs.lchmod()`修改的是软连接。

>fs.chmod(path, mode, callback)
>fs.chmodSync(path, mode)

>fs.fchmod(fd, mode, callback)
>fs.fchmodSync(fd, mode)

>fs.lchmod(path, mode, callback)#
>fs.lchmodSync(path, mode)


例子：

```javascript
var fs = require('fs');

fs.chmod('./fileForChown.txt', '777', function(err){
    if(err) console.log(err);
    console.log('权限修改成功');
});
```

同步版本：

```
var fs = require('fs');

fs.chmodSync('./fileForChown.txt', '777');
```

### 获取文件状态

区别：

* `fs.stat()` vs `fs.fstat()`：传文件路径 vs 文件句柄。
* `fs.stat()` vs `fs.lstat()`：如果文件是软链接，那么`fs.stat()`返回目标文件的状态，`fs.lstat()`返回软链接本身的状态。

>fs.stat(path, callback)
>fs.statSync(path)

>fs.fstat(fd, callback)
>fs.fstatSync(fd)

>fs.lstat(path, callback)
>fs.lstatSync(path)

主要关注`Class: fs.Stats`。

首先是方法

* stats.isFile()  -- 是否文件
* stats.isDirectory() -- 是否目录
* stats.isBlockDevice() -- 什么鬼
* stats.isCharacterDevice() -- 什么鬼
* stats.isSymbolicLink() (only valid with fs.lstat()) -- 什么鬼
* stats.isFIFO() -- 什么鬼
* stats.isSocket() -- 是不是socket文件

官网例子：

```javascript
{
  dev: 2114,
  ino: 48064969,
  mode: 33188,
  nlink: 1,
  uid: 85,
  gid: 100,
  rdev: 0,
  size: 527,
  blksize: 4096,
  blocks: 8,
  atime: Mon, 10 Oct 2011 23:24:11 GMT, // 访问时间
  mtime: Mon, 10 Oct 2011 23:24:11 GMT,  // 文件内容修改时间
  ctime: Mon, 10 Oct 2011 23:24:11 GMT,  // 文件状态修改时间
  birthtime: Mon, 10 Oct 2011 23:24:11 GMT  // 创建时间
}
```

  * atime：Access Time // 访问时间
  * mtime:: Modified Time  // 文件内容修改时间
  * ctime: Changed Time.  // 文件状态修改时间，比如修改文件所有者、修改权限、重命名等
  * birthtime: Birth Time // 创建时间。在某些系统上是不可靠的，因为拿不到。

例子：

```javascript
var fs = require('fs');

var getTimeDesc = function(d){
    return [d.getFullYear(), d.getMonth()+1, d.getDate()].join('-') + ' ' + [d.getHours(), d.getMinutes(), d.getSeconds()].join(':');
};

fs.stat('./fileForStat.txt', function(err, stats){
    console.log('文件大小: ' + stats.size);
    console.log('创建时间: ' + getTimeDesc(stats.birthtime));
    console.log('访问时间: ' + getTimeDesc(stats.atime));
    console.log('修改时间: ' + getTimeDesc(stats.mtime));
});
```

输出如下：

```powershell
/usr/local/bin/node stat.js
文件大小: 3613
创建时间: 2016-7-16 12:40:49
访问时间: 2016-7-16 12:40:49
修改时间: 2016-7-16 12:40:49

Process finished with exit code 0
```

同步的例子：

```javascript
var fs = require('fs');

var getTimeDesc = function(d){
    return [d.getFullYear(), d.getMonth()+1, d.getDate()].join('-') + ' ' + [d.getHours(), d.getMinutes(), d.getSeconds()].join(':');
};

var stats = fs.statSync('./fileForStat.txt');

console.log('文件大小: ' + stats.size);
console.log('创建时间: ' + getTimeDesc(stats.birthtime));
console.log('访问时间: ' + getTimeDesc(stats.atime));
console.log('修改时间: ' + getTimeDesc(stats.mtime));
```

### 访问/权限检测

例子：

```javascript
// fs.access(path[, mode], callback)
var fs = require('fs');

fs.access('./fileForAccess.txt', function(err){
    if(err) throw err;
    console.log('可以访问');
});
```

同步版本：

```javascript
// fs.accessSync(path[, mode])
var fs = require('fs');

// 如果成功，则返回undefined，如果失败，则抛出错误（什么鬼）
try{
    fs.accessSync('./fileForAccess.txt');
}catch(e){
    throw(e);
}
```

### 文件打开/关闭

比较底层的接口，实际需要用到的机会不多。需要用到的时候看下[文档](https://nodejs.org/api/fs.html#fs_fs_open_path_flags_mode_callback)就行。

* flags：文件打开模式，比如`r`、`r+`、`w`、`w+`等。可选模式非常多。
* mode：默认是`666`，可读+可写。

>fs.open(path, flags[, mode], callback)
>fs.openSync(path, flags[, mode])
>fs.close(fd, callback)
>fs.closeSync(fd)

### 文件读取（底层）

相对底层的读取接口，参数如下

* fd：文件句柄。
* buffer：将读取的文件内容写到buffer里。
* offset：buffer开始写入的位置。（在offset开始写入，还是offset+1？）
* length：要读取的字节数。
* position：文件从哪个位置开始读取。如果是null，那么就从当前位置开始读取。（读取操作会记录下上一个位置）

此外，`callback`的回调参数为`(err, bytesRead, buffer)`

>fs.read(fd, buffer, offset, length, position, callback)


### 追加文件内容

>fs.appendFile(file, data[, options], callback)

* file：可以是文件路径，也可以是文件句柄。（还可以是buffer？）
* data：要追加的内容。string或者buffer。
* options
    * encoding：编码，默认是`utf8`
    * mode：默认是`0o666`
    * flag：默认是`a`

注意：如果`file`是文件句柄，那么

* 开始追加数据前，file需要已经打开。
* file需要手动关闭。

```javascript
var fs = require('fs');

fs.appendFile('./extra/fileForAppend.txt', 'helo', 'utf8', function(err){
    if(err) throw err;
    console.log('append成功');
});
```

### 文件内容截取

>fs.truncate(path, len, callback)
>fs.truncateSync(path, len)
>
>fs.ftruncate(fd, len, callback)
>fs.ftruncateSync(fd, len)

用途参考[linux说明文档](http://man7.org/linux/man-pages/man2/ftruncate.2.html)。

要点：

* `offset`不会变化。比如通过`fs.read()`读取文件内容，就需要特别注意。
* 如果`len`小于文件内容长度，剩余文件内容部分会丢失；如果`len`大于文件内容长度，那么超出的部分，会用`\0`进行填充。
* 如果传的是文件路径，需要确保文件是可写的；如果传的是文件句柄，需要确保文件句柄已经打开并且可写入。

>The truncate() and ftruncate() functions cause the regular file named
by path or referenced by fd to be truncated to a size of precisely
length bytes.

>If the file previously was larger than this size, the extra data is
lost.  If the file previously was shorter, it is extended, and the
extended part reads as null bytes ('\0').

>The file offset is not changed.

> With ftruncate(), the file must be open for writing; with truncate(), the file must be writable.

### 修改文件属性（时间）

* path/fd：文件路径/文件句柄
* atime：Access Time。上一次访问文件数据的时间。
* mtime：Modified Time。修改时间。

>fs.utimes(path, atime, mtime, callback)
>fs.utimesSync(path, atime, mtime)

>fs.futimes(fd, atime, mtime, callback)
>fs.futimesSync(fd, atime, mtime)

备注，在命令行下可以

* 通过`stat`查看文件的状态信息，包括了上面的atime、mtime。
* 通过`touch`修改这几个时间。

### 创建文件链接

>fs.symlink(target, path[, type], callback)
>fs.symlinkSync(target, path[, type])
>
>fs.link(srcpath, dstpath, callback)
>fs.linkSync(srcpath, dstpath)

>  link() creates a new link (also known as a hard link) to an existing file.
       
软链接、硬链接区别：[参考](https://www.ibm.com/developerworks/cn/linux/l-cn-hardandsymb-links/) 或者 [这个]。(http://www.cnblogs.com/itech/archive/2009/04/10/1433052.html)

* 硬链接：inode相同，多个别名。删除一个硬链接文件，不会影响其他有相同inode的文件。
* 软链接：有自己的inode，用户数据块存放指向文件的inode。
       
参考[这里](http://man7.org/linux/man-pages/man2/link.2.html)。

### 创建临时目录

>fs.mkdtemp(prefix, callback)
>fs.mkdtempSync(prefix)

备忘：跟普通的随便找个目录，创建个随机名字的文件夹，有什么区别？

代码示例如下：

```javascript
var fs = require('fs');

fs.mkdtemp('/tmp/', function(err, folder){
    if(err) throw err;
    console.log('创建临时目录: ' + folder);
});
```

输出如下：

```powershell
/usr/local/bin/node mkdtemp.js
创建临时目录: /tmp/Cxw51O
```

### 找出软连接指向的真实路径

>fs.readlink(path[, options], callback)
>fs.readlinkSync(path[, options])

如下面例子，创建了个软链接指向`fileForReadLink.txt`，通过`fs.readlink()`就可以找出原始的路径。

```javascript
var fs = require('fs');
var randomFileName = './extra/fileForReadLink-' + String(Math.random()).slice(2, 6) + '.txt';

fs.symlinkSync('./extra/fileForReadLink.txt', randomFileName);
fs.readlink(randomFileName, 'utf8', function(err, linkString){
    if(err) throw err;
    console.log('链接文件内容: ' + linkString);
});
```

类似终端下直接运行`readlink`。对于软链接文件，效果同上面代码。对于硬链接，没有输出。

```powershell
➜  extra git:(master) ✗ readlink fileForReadLink-9827.txt
./extra/fileForReadLink.txt
➜  extra git:(master) ✗ readlink fileForLinkHard.txt 
➜  extra git:(master) ✗ readlink fileForLinkSoft.txt
./extra/fileForLink.txt
```

### 真实路径

>fs.realpath(path[, options], callback)
>fs.realpathSync(path[, options])

例子：（不能作用于软链接？）

```javascript
var fs = require('fs');
var path = require('path');

// fileForRealPath1.txt 是普通文件,正常运行
fs.realpath('./extra/inner/fileForRealPath1.txt', function(err, resolvedPath){
    if(err) throw err;
    console.log('fs.realpath: ' + resolvedPath);
});

// fileForRealPath.txt 是软链接, 会报错,提示找不到文件
fs.realpath('./extra/inner/fileForRealPath.txt', function(err, resolvedPath){
    if(err) throw err;
    console.log('fs.realpath: ' + resolvedPath);
});

console.log( 'path.resolve: ' + path.resolve('./extra/inner/fileForRealpath.txt') );
```

输出如下：

```powershell
path.resolve: /Users/a/Documents/git-code/git-blog/demo/2015.05.21-node-basic/fs/extra/inner/fileForRealpath.txt
fs.realpath: /Users/a/Documents/git-code/git-blog/demo/2015.05.21-node-basic/fs/extra/inner/fileForRealPath1.txt
/Users/a/Documents/git-code/git-blog/demo/2015.05.21-node-basic/fs/realpath.js:12
    if(err) throw err;
            ^

Error: ENOENT: no such file or directory, realpath './extra/inner/fileForRealPath.txt'
    at Error (native)

Process finished with exit code 1
```

### 删除目录

>fs.rmdir(path, callback)
>fs.rmdirSync(path)

例子如下：
```javascript
var fs = require('fs');

fs.rmdir('./dirForRemove', function(err){
    if(err) throw err;
    console.log('目录删除成功');
});
```

### 不常用

#### 缓冲区内容写到磁盘

>fs.fdatasync(fd, callback)
>fs.fdatasyncSync(fd)

可以参考这里：

>1、sync函数
sync函数只是将所有修改过的块缓冲区排入写队列，然后就返回，它并不等待实际写磁盘操作结束。
通常称为update的系统守护进程会周期性地（一般每隔30秒）调用sync函数。这就保证了定期冲洗内核的块缓冲区。命令sync(1)也调用sync函数。
2、fsync函数
fsync函数只对由文件描述符filedes指定的单一文件起作用，并且等待写磁盘操作结束，然后返回。
fsync可用于数据库这样的应用程序，这种应用程序需要确保将修改过的块立即写到磁盘上。
3、fdatasync函数
fdatasync函数类似于fsync，但它只影响文件的数据部分。而除数据外，fsync还会同步更新文件的属性。
对于提供事务支持的数据库，在事务提交时，都要确保事务日志（包含该事务所有的修改操作以及一个提交记录）完全写到硬盘上，才认定事务提交成功并返回给应用层。

## 06 网络服务-http

大多数nodejs开发者都是冲着开发web server的目的选择了nodejs。正如官网所展示的，借助http模块，可以几行代码就搞定一个超迷你的web server。

在nodejs中，`http`可以说是最核心的模块，同时也是比较复杂的一个模块。上手很简单，但一旦深入学习，不少初学者就会觉得头疼，不知从何入手。

本文先从一个简单的例子出发，引出`http`模块最核心的四个实例。看完本文，应该就能够对http模块有个整体的认识。

### 一个简单的例子

在下面的例子中，我们创建了1个web服务器、1个http客户端

* 服务器server：接收来自客户端的请求，并将客户端请求的地址返回给客户端。
* 客户端client：向服务器发起请求，并将服务器返回的内容打印到控制台。

代码如下所示，只有几行，但包含了不少信息量。下一小节会进行简单介绍。

```js
var http = require('http');

// http server 例子
var server = http.createServer(function(serverReq, serverRes){
    var url = serverReq.url;
    serverRes.end( '您访问的地址是：' + url );
});

server.listen(3000);

// http client 例子
var client = http.get('http://127.0.0.1:3000', function(clientRes){
    clientRes.pipe(process.stdout);
});

```

### 例子解释

在上面这个简单的例子里，涉及了4个实例。大部分时候，serverReq、serverRes 才是主角。

* server：http.Server实例，用来提供服务，处理客户端的请求。
* client：http.ClientReques实例，用来向服务端发起请求。
* serverReq/clientRes：其实都是 http.IncomingMessage实。serverReq 用来获取客户端请求的相关信息，如request header；而clientRes用来获取服务端返回的相关信息，比如response header。
* serverRes：http.ServerResponse实例

### 关于http.IncomingMessage、http.ServerResponse

先讲下 http.ServerResponse 实例。作用很明确，服务端通过http.ServerResponse 实例，来个请求方发送数据。包括发送响应表头，发送响应主体等。

接下来是 http.IncomingMessage 实例，由于在 server、client 都出现了，初学者难免有点迷茫。它的作用是

在server端：获取请求发送方的信息，比如请求方法、路径、传递的数据等。
在client端：获取 server 端发送过来的信息，比如请求方法、路径、传递的数据等。

http.IncomingMessage实例 有三个属性需要注意：method、statusCode、statusMessage。

* method：只在 server 端的实例有（也就是 serverReq.method）
* statusCode/statusMessage：只在 client 端 的实例有（也就是 clientRes.method）

### 关于继承与扩展

#### http.Server

* http.Server 继承了 net.Server （于是顺带需要学一下 net.Server 的API、属性、相关事件）
* net.createServer(fn)，回调中的 `socket` 是个双工的stream接口，也就是说，读取发送方信息、向发送方发送信息都靠他。

备注：socket的客户端、服务端是相对的概念，所以其实 net.Server 内部也是用了 net.Socket（不负责任猜想）

```js
// 参考：https://cnodejs.org/topic/4fb1c1fd1975fe1e1310490b
var net = require('net');

var PORT = 8989;
var HOST = '127.0.0.1';

var server = net.createServer(function(socket){
    console.log('Connected: ' + socket.remoteAddress + ':' + socket.remotePort);
    
    socket.on('data', function(data){
        console.log('DATA ' + socket.remoteAddress + ': ' + data);
        console.log('Data is: ' + data);

        socket.write('Data from you is  "' + data + '"');
    });

    socket.on('close', function(){
         console.log('CLOSED: ' +
            socket.remoteAddress + ' ' + socket.remotePort);
    });
});
server.listen(PORT, HOST);

console.log(server instanceof net.Server);  // true
```

#### http.ClientRequest

http.ClientRequest 内部创建了一个socket来发起请求，[代码如下](https://github.com/nodejs/node/blob/master/lib/_http_client.js#L174)。

当你调用 http.request(options) 时，内部是这样的

```javascript
self.onSocket(net.createConnection(options));

```

#### http.ServerResponse 

* 实现了 Writable Stream interface，内部也是通过socket来发送信息。

#### http.IncomingMessage 

* 实现了 Readable Stream interface，参考[这里](https://github.com/nodejs/node/blob/master/lib/_http_incoming.js#L62)
* req.socket --> 获得跟这次连接相关的socket

## 07 网络服务 http-req请求

### 概览

本文的重点会放在`req`这个对象上。前面已经提到，它其实是http.IncomingMessage实例，在服务端、客户端作用略微有差异

* 服务端处：获取请求方的相关信息，如request header等。
* 客户端处：获取响应方返回的相关信息，如statusCode等。

服务端例子：

```js
// 下面的 req
var http = require('http');
var server = http.createServer(function(req, res){
    console.log(req.headers);
    res.end('ok');
});
server.listen(3000);
```

客户端例子

```js
// 下面的res
var http = require('http');
http.get('http://127.0.0.1:3000', function(res){
    console.log(res.statusCode);
});
```

### 属性/方法/事件 分类

http.IncomingMessage的属性/方法/事件 不是特别多，按照是否客户端/服务端 特有的，下面进行简单归类。可以看到

* 服务端处特有：url
* 客户端处特有：statusCode、statusMessage

| 类型      |     名称 |   服务端   |  客户端  |
| :-------- | :--------:| :------: | :---: |
| 事件    |   aborted |  ✓  |   ✓   |
| 事件    |   close |  ✓  |   ✓   |
| 属性    |   headers |  ✓  |   ✓   |
| 属性    |   rawHeaders |  ✓  |   ✓   |
| 属性    |   statusCode |  ✕  |   ✓   |
| 属性    |   statusMessage |  ✕  |   ✓   |
| 属性    |   httpVersion |  ✓  |   ✓   |
| 属性    |   httpVersion |  ✓  |   ✓   |
| 属性    |   url |  ✓  |   ✕   |
| 属性    |   socket |  ✓  |   ✓   |
| 方法    |   .destroy() |  ✓  |   ✓   |
| 方法    |   .setTimeout() |  ✓  |   ✓   |

### 服务端的例子

#### 例子一：获取httpVersion/method/url

下面是一个典型的HTTP请求报文，里面最重要的内容包括：HTTP版本、请求方法、请求地址、请求头部。

```http
GET /hello HTTP/1.1
Host: 127.0.0.1:3000
Connection: keep-alive
Cache-Control: no-cache
```

那么，如何获取上面提到的信息呢？很简单，直接上代码

```js
// getClientInfo.js
var http = require('http');

var server = http.createServer(function(req, res){
    console.log( '1、客户端请求url：' + req.url );
    console.log( '2、http版本：' + req.httpVersion );
    console.log( '3、http请求方法：' + req.method );
    console.log( '4、http请求头部' + JSON.stringify(req.headers) );

    res.end('ok');
});

server.listen(3000);
```

效果如下：

```bash
1、客户端请求url：/hello
2、http版本：1.1
3、http请求方法：GET
4、http headers：{"host":"127.0.0.1:3000","connection":"keep-alive","cache-control":"no-cache","user-agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.71 Safari/537.36","postman-token":"1148986a-ddfb-3569-e2c0-585634655fe4","accept":"*/*","accept-encoding":"gzip, deflate, sdch, br","accept-language":"zh-CN,zh;q=0.8,en;q=0.6,zh-TW;q=0.4"}
```

#### 例子二：获取get请求参数

服务端代码如下：

```js
// getClientGetQuery.js
var http = require('http');
var url = require('url');
var querystring = require('querystring');

var server = http.createServer(function(req, res){
    var urlObj = url.parse(req.url);
    var query = urlObj.query;
    var queryObj = querystring.parse(query);
    
    console.log( JSON.stringify(queryObj) );
    
    res.end('ok');
});

server.listen(3000);
```

访问地址 http://127.0.0.1:3000/hello?nick=chyingp&hello=world

服务端输出如下

```bash
{"nick":"chyingp","hello":"world"}
```

#### 例子三：获取post请求参数

服务端代码如下

```js
// getClientPostBody.js
var http = require('http');
var url = require('url');
var querystring = require('querystring');

var server = http.createServer(function(req, res){
    
    var body = '';  
    req.on('data', function(thunk){
        body += thunk;
    });

    req.on('end', function(){
        console.log( 'post body is: ' + body );
        res.end('ok');
    }); 
});

server.listen(3000);
```

通过curl构造post请求：

```bash
curl -d 'nick=casper&hello=world' http://127.0.0.1:3000
```

服务端打印如下：

```bash
post body is: nick=casper&hello=world
```

备注：post请求中，不同的`Content-type`，post body有不小差异，感兴趣的同学可以研究下。

本例中的post请求，HTTP报文大概如下

```http
POST / HTTP/1.1
Host: 127.0.0.1:3000
Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache

nick=casper&hello=world
```

### 客户端处例子

#### 例子一：获取httpVersion/statusCode/statusMessage

代码如下：

```js
var http = require('http');
var server = http.createServer(function(req, res){
    res.writeHead(200, {'content-type': 'text/plain',});
    res.end('from server');
});
server.listen(3000);

var client = http.get('http://127.0.0.1:3000', function(res){
    console.log('1、http版本：' + res.httpVersion);
    console.log('2、返回状态码：' + res.statusCode);
    console.log('3、返回状态描述信息：' + res.statusMessage);
    console.log('4、返回正文：');

    res.pipe(process.stdout);
});
```

控制台输出：

```bash
1、http版本：1.1
2、返回状态码：200
3、返回状态描述信息：OK
4、返回正文：
from server
```

### 事件对比：aborted、close

官方文档对这两个事件的解释是：当客户端终止请求时，触发aborted事件；当客户端连接断开时，触发close事件；官方文档传送们：[地址](https://nodejs.org/api/http.html#http_event_aborted_1)

解释得比较含糊，从实际实验对比上来看，跟官方文档有不小出入。此外，客户端处、服务端处的表现也是不同的。

#### 服务端表现

根据实际测试结果来看，当客户端：

* abort请求时，服务端req的aborted、close事件都会触发；（诡异）
* 请求正常完成时，服务端req的close事件不会触发；（也很诡异）

直接扒了下nodejs的源代码，发现的确是同时触发的，触发场景：请求正常结束前，客户端abort请求。

测试代码如下：

```js
var http = require('http');

var server = http.createServer(function(req, res){
    
    console.log('1、收到客户端请求: ' + req.url);
    
    req.on('aborted', function(){
        console.log('2、客户端请求aborted');
    });
    
    req.on('close', function(){
        console.log('3、客户端请求close');
    });
    
    // res.end('ok'); 故意不返回，等着客户端中断请求
});

server.listen(3000, function(){
    var client = http.get('http://127.0.0.1:3000/aborted');
    setTimeout(function(){
        client.abort();  // 故意延迟100ms，确保请求发出
    }, 100);    
});


// 输出如下
// 1、收到客户端请求: /aborted
// 2、客户端请求aborted
// 3、客户端请求close
```

以下代码来自nodejs源码（_http_server.js）

```js
  function abortIncoming() {
    while (incoming.length) {
      var req = incoming.shift();
      req.emit('aborted');
      req.emit('close');
    }
    // abort socket._httpMessage ?
  }
```

再来一波对比，`req.on('close')`和`req.socket.on('close')`。

```js
// reqSocketClose.js
var http = require('http');

var server = http.createServer(function(req, res){
    
    console.log('server: 收到客户端请求');
    
    req.on('close', function(){
        console.log('server: req close');
    });
    
    req.socket.on('close', function(){
        console.log('server: req.socket close');
    });    
    
    res.end('ok'); 
});

server.listen(3000);

var client = http.get('http://127.0.0.1:3000/aborted', function(res){
    console.log('client: 收到服务端响应');
});
```

输出如下，正儿八经的close事件触发了。

```bash
server: 收到客户端请求
server: req.socket close
client: 收到服务端响应
```

#### 客户端表现

猜测客户端的aborted、close也是在类似场景下触发，测试代码如下。发现一个比较有意思的情况，`res.pipe(process.stdout)` 这行代码是否添加，会影响`close`是否触发。

* 没有`res.pipe(process.stdout)`：close不触发。
* 有`res.pipe(process.stdout)`：close触发。

```js
var http = require('http');

var server = http.createServer(function(req, res){
    
    console.log('1、服务端：收到客户端请求');
    
    res.flushHeaders();
    res.setTimeout(100);    // 故意不返回，3000ms后超时
});


server.on('error', function(){});

server.listen(3000, function(){

    var client = http.get('http://127.0.0.1:3000/aborted', function(res){

        console.log('2、客户端：收到服务端响应');

        // res.pipe(process.stdout); 注意这行代码
        
        res.on('aborted', function(){
            console.log('3、客户端：aborted触发！');
        });

        res.on('close', function(){
            console.log('4、客户端：close触发！');
        });     
    });
});
```

### 信息量略大的 .destroy()

经过前面aborted、close的摧残，本能的觉得 .destroy() 方法的表现会有很多惊喜之处。

测试代码如下：

```js
var http = require('http');

var server = http.createServer(function(req, res){
    
    console.log('服务端：收到客户端请求');
    
    req.destroy(new Error('测试destroy'));
    
    req.on('error', function(error){
        console.log('服务端：req error: ' + error.message);
    });
    
    req.socket.on('error', function(error){
        console.log('服务端：req socket error: ' + error.message);
    })
});

server.on('error', function(error){
    console.log('服务端：server error: ' + error.message);
});

server.listen(3000, function(){

    var client = http.get('http://127.0.0.1:3000/aborted', function(res){
        // do nothing
    });

    client.on('error', function(error){
        console.log('客户端：client error触发！' + error.message);
    });
});
```

输出如下。根据 .destroy() 调用的时机不同，error 触发的对象不同。（测试过程比较枯燥，有时间再总结一下）

```bash
服务端：收到客户端请求
服务端：req socket error: 测试destroy
客户端：client error触发！socket hang up
```

### 不常用属性

* rawHeaders：未解析前的request header。
* trailers：在分块传输编码(chunk)中用到，表示trailer后的header可分块传输。（感兴趣的可以研究下）
* rawTrailers：

关于trailers属性：
>The request/response trailers object. Only populated at the 'end' event.

### 写在后面

一个貌似很简单的对象，实际比想的要复杂一些。做了不少对比实验，也发现了一些好玩的东西，打算深入学习的同学可以自己多动手尝试一下 :)

TODO：

1. 对close、aborted进行更深入对比
2. 对.destroy()进行更深入对比

## 08 网络服务 http-res响应

### 概览

http模块四剑客之一的`res`，应该都不陌生了。一个web服务程序，接受到来自客户端的http请求后，向客户端返回正确的响应内容，这就是`res`的职责。

返回的内容包括：状态代码/状态描述信息、响应头部、响应主体。下文会举几个简单的例子。

```js
var http = require('http');
var server = http.createServer(function(req, res){
    res.send('ok');
});
server.listen(3000);
```

### 例子

在下面的例子中，我们同时设置了 状态代码/状态描述信息、响应头部、响应主体，就是这么简单。

```js
var http = require('http');

// 设置状态码、状态描述信息、响应主体
var server = http.createServer(function(req, res){
    res.writeHead(200, 'ok', {
        'Content-Type': 'text/plain'
    });
    res.end('hello');
});

server.listen(3000);
```

#### 设置状态代码、状态描述信息

`res`提供了 res.writeHead()、res.statusCode/res.statusMessage 来实现这个目的。

举例，如果想要设置 200/ok ，可以

```js
res.writeHead(200, 'ok');
```

也可以

```js
res.statusCode = 200;
res.statusMessage = 'ok';
```

两者差不多，差异点在于

1. res.writeHead() 可以提供额外的功能，比如设置响应头部。
2. 当响应头部发送出去后，res.statusCode/res.statusMessage 会被设置成已发送出去的 状态代码/状态描述信息。

#### 设置响应头部

`res`提供了 res.writeHead()、response.setHeader() 来实现响应头部的设置。

举例，比如想把 `Content-Type` 设置为 `text-plain`，那么可以

```js
// 方法一
res.writeHead(200, 'ok', {
    'Content-Type': 'text-plain'
});

// 方法二
res.setHeader('Content-Type', 'text-plain');
```

两者的差异点在哪里呢？

1. res.writeHead() 不单单是设置header。
2. 已经通过 res.setHeader() 设置了header，当通过 res.writeHead() 设置同名header，res.writeHead() 的设置会覆盖之前的设置。

关于第2点差异，这里举个例子。下面代码，最终的 `Content-Type` 为 `text/plain`。

```js
var http = require('http');

var server = http.createServer(function(req, res){
    res.setHeader('Content-Type', 'text/html');
    res.writeHead(200, 'ok', {
        'Content-Type': 'text/plain'
    });
    res.end('hello');
});

server.listen(3000);
```

而下面的例子，则直接报错。报错信息为 `Error: Can't set headers after they are sent.`。

```js
var http = require('http');

var server = http.createServer(function(req, res){    
    res.writeHead(200, 'ok', {
        'Content-Type': 'text/plain'
    });
    res.setHeader('Content-Type', 'text/html');
    res.end('hello');
});

server.listen(3000);
```

#### 其他响应头部操作

增、删、改、查 是配套的。下面分别举例说明下，例子太简单就直接上代码了。

```js
// 增
res.setHeader('Content-Type', 'text/plain');

// 删
res.removeHeader('Content-Type');

// 改
res.setHeader('Content-Type', 'text/plain');
res.setHeader('Content-Type', 'text/html');  // 覆盖

// 查
res.getHeader('content-type');
```

其中略显不同的是 res.getHeader(name)，name 用的是小写，返回值没做特殊处理。

```js
res.setHeader('Content-Type', 'TEXT/HTML');
console.log( res.getHeader('content-type') );  // TEXT/HTML

res.setHeader('Content-Type', 'text/plain');
console.log( res.getHeader('content-type') );  // text/plain
```

此外，还有不那么常用的：

* res.headersSent：header是否已经发送；
* res.sendDate：默认为true。但为true时，会在response header里自动设置Date首部。

### 设置响应主体

主要用到 res.write() 以及 res.end() 两个方法。

res.write() API的信息量略大，建议看下[官方文档](https://nodejs.org/api/http.html#http_response_write_chunk_encoding_callback)。

#### response.write(chunk[, encoding][, callback])

* chunk：响应主体的内容，可以是string，也可以是buffer。当为string时，encoding参数用来指明编码方式。（默认是utf8）
* encoding：编码方式，默认是 utf8。
* callback：当响应体flushed时触发。（TODO 这里想下更好的解释。。。）

使用上没什么难度，只是有些注意事项：

1. 如果 res.write() 被调用时， res.writeHead() 还没被调用过，那么，就会把header flush出去。
2. res.write() 可以被调用多次。
3. 当 res.write(chunk) 第一次被调用时，node 会将 header 信息 以及 chunk 发送到客户端。第二次调用 res.write(chunk) ，node 会认为你是要streaming data（WTF，该怎么翻译）。。。

>Returns true if the entire data was flushed successfully to the kernel buffer. Returns false if all or part of the data was queued in user memory. 'drain' will be emitted when the buffer is free again.

#### response.end([data][, encoding][, callback])

掌握了 res.write() 的话，res.end() 就很简单了。res.end() 的用处是告诉nodejs，header、body都给你了，这次响应就到这里吧。

有点像个语法糖，可以看成下面两个调用的组合。至于callback，当响应传递结束后触发。

```js
res.write(data, encoding);
res.end()
```

### chunk数据

参考这里：http://stackoverflow.com/questions/6258210/how-can-i-output-data-before-i-end-the-response

也就是说，除了nodejs的特性，还需要了解 HTTP协议、浏览器的具体实现。（细思极恐）

如果是 `text/html`

```js
var http = require('http');

http.createServer(function(req, res) {    
    res.setHeader('Content-Type', 'text/html; charset=utf-8');
    res.write('hello');

    setTimeout(function() {
        res.write(' world!');
        res.end();
    }, 2000);

}).listen(3000);
```

如果是 `text/plain`

```js
var http = require('http');

http.createServer(function (req, res) {
    res.writeHead(200, {
        'Content-Type': 'text/plain; charset=utf-8',
        'X-Content-Type-Options': 'nosniff'
    });
    res.write('hello');

    setTimeout(function(){
        res.write('world');
        res.end()
    }, 2000);
    
}).listen(3000);
```

失败例子

```js
var http = require('http');

var server = http.createServer(function(req, res){
    res.writeHead(200, 'ok', {
        'Content-Type': 'text/html'
    });
    res.write('hello');
    
    setTimeout(function(){
        res.write('world');
        res.end();
    }, 2000);
});

server.listen(3000);
```

### 超时处理

接口：response.setTimeout(msecs, callback)

关于 timeout 事件的说明，同样是言简意赅（WTF），话少信息量大，最好来个demo TODO

>If no 'timeout' listener is added to the request, the response, or the server, then sockets are destroyed when they time out. If you assign a handler on the request, the response, or the server's 'timeout' events, then it is your responsibility to handle timed out sockets.

### 事件 close/finish

* close：response.end() 被调用前，连接就断开了。此时会触发这个事件。
* finish：响应header、body都已经发送出去（交给操作系统，排队等候传输），但客户端是否实际收到数据为止。（这个事件后，res 上就不会再有其他事件触发）

### 其他不常用属性/方法

* response.finished：一开始是false，响应结束后，设置为true。
* response.sendDate：默认是true。是否自动设置Date头部。（按HTTP协议是必须要的，除非是调试用，不然不要设置为false）
* response.headersSent：只读属性。响应头部是否已发送。
* response.writeContinue()：发送  HTTP/1.1 100 Continue 消息给客户端，提示说服务端愿意接受客户端的请求，请继续发送请求正文（body)。（TODO 做个demo啥的是大大的好）


## 09 网络服务-http-client


### ClientRequest概览

当你调用 http.request(options) 时，会返回 ClientRequest实例，主要用来创建HTTP客户端请求。

在前面的章节里，已经对http模块的的其他方面进行了不少介绍，如http.Server、http.ServerResponse、http.IncomingMessage。

有了前面的基础，详细本文不难理解，本文更多的以例子为主。

### 简单的GET请求

下面构造了个GET请求，访问 http://id.qq.com/ ，并将返回的网页内容打印在控制台下。

```js
var http = require('http');
var options = {
    protocol: 'http:',
    hostname: 'id.qq.com',
    port: '80',
    path: '/',
    method: 'GET'
};

var client = http.request(options, function(res){
    var data = '';
    res.setEncoding('utf8');
    res.on('data', function(chunk){
        data += chunk;
    });
    res.on('end', function(){
        console.log(data);
    });
});

client.end();
```

当然，也可以用便捷方法 http.get(options) 进行重写

```js
var http = require('http');

http.get('http://id.qq.com/', function(res){
    var data = '';
    res.setEncoding('utf8');
    res.on('data', function(chunk){
        data += chunk;
    });
    res.on('end', function(){
        console.log(data);
    });
});
```

### 简单的post请求

在下面例子中，首先创建了个http server，负责将客户端发送过来的数据回传。

接着，创建客户端POST请求，向服务端发送数据。需要注意的点有：

1. method 指定为 POST。
2. headers 里声明了 content-type 为 application/x-www-form-urlencoded。
3. 数据发送前，用 querystring.stringify(obj) 对传输的对象进行了格式化。

```js
var http = require('http');
var querystring = require('querystring');

var createClientPostRequest = function(){
    var options = {
        method: 'POST',
        protocol: 'http:',
        hostname: '127.0.0.1',
        port: '3000',
        path: '/post',
        headers: {
            "connection": "keep-alive",
            "content-type": "application/x-www-form-urlencoded"
        }    
    };

    // 发送给服务端的数据
    var postBody = {
        nick: 'chyingp'
    };

    // 创建客户端请求
    var client = http.request(options, function(res){
        // 最终输出：Server got client data: nick=chyingp
        res.pipe(process.stdout);  
    });

    // 发送的报文主体，记得先用 querystring.stringify() 处理下
    client.write( querystring.stringify(postBody) );
    client.end();
};

// 服务端程序，只是负责回传客户端数据
var server = http.createServer(function(req, res){
    res.write('Server got client data: ');
    req.pipe(res);
});

server.listen(3000, createClientPostRequest);
```

### 各种事件

在官方文档里，http.RequestClient相关的事件共有7个。跟HTTP协议密切相关的有3个，分别是 connect、continue、upgrade，其他4个分别是 abort、aborted、socket、response。

* 其他：abort、aborted、socket、response
* 与HTTP协议相关：connect、continue、upgrade

跟HTTP协议相关的会相对复杂些，因为涉及HTTP协议的设计细节。其他3个相对简单。下面分别进行简单的介绍。

#### response事件

最容易理解的一个，当收到来自服务端的响应时触发，其实跟 http.get(url, cbk) 中的回调是一样的，看下程序运行的打印信息就知道。

```js
var http = require('http');

var url = 'http://id.qq.com/';

var client = http.get(url, function(res){
    console.log('1. response event');
});

client.on('response', function(res){
    console.log('2. response event');
});

client.end();
```

打印信息：

```bash
1. response event
2. response event
```

#### socket事件

当给client分配socket的时候触发，如果熟悉net模块对这个事件应该不陌生。大部分时候并不需要关注这个事件，虽然内部其实挺复杂的。

#### abort/aborted 事件

这两个事件看着非常像，都是请求中断时触发，差异在于中断的发起方：

* abort：客户端主动中断请求（第一次调用 client.abort() 时触发）
* aborted：服务端主动中断请求，且请求已经中断时触发。

#### continue事件

当收到服务端的响应 `100 Continue` 时触发。熟悉HTTP协议的同学应该对 `100 Continue` 有所了解。当客户端向服务端发送首部 `Expect: 100-continue` ，服务端经过一定的校验后，决定对客户端的后续请求放行，于是返回返回 `100 Continue`，知会客户端，可以继续发送数据。（request body）

#### upgrade事件

同样是跟HTTP协议密切相关。当客户端向客户端发起请求时，可以在请求首部里声明 `'Connection': 'Upgrade'` ，以此要求服务端，将当前连接升级到新的协议。如果服务器同意，那么就升级协议继续通信。这里不打算展开太多细节，直接上官方文档的代码

```js
const http = require('http');

// Create an HTTP server
var srv = http.createServer( (req, res) => {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('okay');
});
srv.on('upgrade', (req, socket, head) => {
  socket.write('HTTP/1.1 101 Web Socket Protocol Handshake\r\n' +
               'Upgrade: WebSocket\r\n' +
               'Connection: Upgrade\r\n' +
               '\r\n');

  socket.pipe(socket); // echo back
});

// now that server is running
srv.listen(1337, '127.0.0.1', () => {

  // make a request
  var options = {
    port: 1337,
    hostname: '127.0.0.1',
    headers: {
      'Connection': 'Upgrade',
      'Upgrade': 'websocket'
    }
  };

  var req = http.request(options);
  req.end();

  req.on('upgrade', (res, socket, upgradeHead) => {
    console.log('got upgraded!');
    socket.end();
    process.exit(0);
  });
});
```

### 其他

除了上面讲解到的属性、方法、事件外，还有下面方法没有讲到。并不是它们不重要，篇幅有限，后面再展开。

* client.abort()：中断请求；
* client.setTimeout(timeout)：请求超时设置；
* client.flushHeaders() 及早将请求首部发送出去；
* client.setSocketKeepAlive()：当内部分配 socket 并连接上时，就会内部调用 socket.keepAlive()；
* client.setNoDelay([noDelay])：当内部分配 socket 并连接上时，就会内部调用 socket.setNoDelay()；



## 10 网络服务-http-server

### http服务端概览

### 创建server

几行代码搞定

```js
var http = require('http');
var requestListener = function(req, res){
    res.end('ok');
};
var server = http.createServer(requestListener);
// var server = new http.Server(requestListener); 跟上面是等价的
server.listen(3000);
```

### 获取请求方信息

#### HTTP版本、HTTP method、headers、url

```js
var http = require('http');

var server = http.createServer(function(req, res){
    console.log('客户端请求url：' + req.url);
    console.log('http版本：' + req.httpVersion);
    console.log('http请求方法：' + req.method);

    res.end('ok');
});

server.listen(3000);
```

效果如下：

```bash
客户端请求url：/hello
http版本：1.1
http请求方法：GET
http headers：{"host":"127.0.0.1:3000","connection":"keep-alive","cache-control":"max-age=0","upgrade-insecure-requests":"1","user-agent":"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.71 Safari/537.36","accept":"text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8","accept-encoding":"gzip, deflate, sdch, br","accept-language":"zh-CN,zh;q=0.8,en;q=0.6,zh-TW;q=0.4"}
```

#### 获取get请求参数

```js
var http = require('http');
var url = require('url');
var querystring = require('querystring');

var server = http.createServer(function(req, res){
    var urlObj = url.parse(req.url);
    var query = urlObj.query;
    var queryObj = querystring.parse(query);
    
    console.log( JSON.stringify(queryObj) );
    
    res.end('ok');
});

server.listen(3000);
```

运行如下命令

```bash
curl http://127.0.0.1:3000/hello\?nick\=chyingp\&hello\=world
```

服务端输出如下

```bash
{"nick":"chyingp","hello":"world"}
```


#### 获取post请求参数

代码如下

```js
var http = require('http');
var url = require('url');
var querystring = require('querystring');

var server = http.createServer(function(req, res){
    
    var body = '';  
    req.on('data', function(thunk){
        body += thunk;
    });

    req.on('end', function(){
        console.log( 'post body is: ' + body );
        res.end('ok');
    }); 
});

server.listen(3000);
```

通过curl构造极简post请求

```bash
curl -d 'nick=casper&hello=world' http://127.0.0.1:3000
```

服务端打印如下。注意，在post请求中，不同的`Content-type`，post body有不小差异，感兴趣的同学可以自己试下。

```bash
post body is: nick=casper&hello=world
```

比如本例中的post请求，HTTP报文大概如下

```http
POST / HTTP/1.1
Host: 127.0.0.1:3000
Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache

nick=casper&hello=world
```


### 枯燥的事件

首先，我们来看下有哪些事件

checkContinue、checkExpectation、clientError、close、connect、connection、request、upgrade

#### error

```js
var http = require('http');
var PORT = 3000;
var noop = function(){};

var svr = http.createServer(noop);
var anotherSvr = http.createServer(noop);

anotherSvr.on('error', function(e){
    console.error('出错啦！' + e.message);
});

svr.listen(PORT, function(){
    anotherSvr.listen(PORT);
});
```

运行代码，输出如下

```bash
出错啦！listen EADDRINUSE :::3000
```

#### connect vs connection

两者差别非常大，虽然字眼看着有点像。

* connect：当客户端的HTTP method为connect时触发。
* connection：当TCP连接建立时触发，大部分时候可以忽略这个事件（目测模块内部自己用到而已）。此外，可以通过 req.connection 来获取这个socket（从nodejs源码来看，req.socket、req.connection 都指向了这个socket）。此外，socket上的readable事件不会触发（具体原因请看模块内部实现，反正我是还没研究）

大部分时候都不会用到，除非你要开发HTTP代理。当客户端发起 connect 请求时触发（注意绕过了 requestListener）

```js
var http = require('http');
var PORT = 3000;

var server = http.createServer(function(req, res){
    res.end('ok');
});

// 注意：发起connect请求的例子在 ./httpServerEventConnectClient.js 里
server.on('connect', function(req, socket, head){
    console.log('connect事件触发');
    socket.end();   // 反正我就只想举个例子，没打算正经处理。。。
});

server.listen(PORT);
```

#### request

当有新的连接到来时触发。那跟 connection 有什么区别呢？

好了，`keep-alive`闪亮登场！在持久化连接的情况下，多个 request 可能对应的是 一个 connection。

先来看下没有`keep-alive`的场景

```js
var http = require('http');
var PORT = 3000;
var requestIndex = 0;
var connectionIndex = 0;

var server = http.createServer(function(req, res){
    res.end('ok');
});

server.on('request', function(req, res){
    requestIndex++;
    console.log('request event: 第'+ requestIndex +'个请求！');
});

server.on('connection', function(req, res){
    connectionIndex++;
    console.log('connection event: 第'+ connectionIndex +'个请求！');
});

server.listen(PORT);
```

通过curl连续发送3个请求，看下效果

```bash
for i in `seq 1 3`; do curl http://127.0.0.1:3000; done
```

服务端输出如下

```bash
connection event: 第1个请求！
request event: 第1个请求！
connection event: 第2个请求！
request event: 第2个请求！
connection event: 第3个请求！
request event: 第3个请求！
```

然后，再来看下有`keep-alive`的场景。用 postman 构造包含 keep-alive 的请求，最终的HTTP请求报文如下

```http
GET / HTTP/1.1
Host: 127.0.0.1:3000
Connection: keep-alive
Cache-Control: no-cache
Postman-Token: 6027fda7-f936-d3ac-e54f-dafcbf5e58ff
```

连续发送3个请求，服务端打印日志如下

```bash
connection event: 第1个请求！
request event: 第1个请求！
request event: 第2个请求！
request event: 第3个请求！
```

### 不常用接口

#### server.close([callback]); 

关闭服务器。其实就是 (new net.Server()).close()，停止接受新的连接。
已经连接上的请求会继续处理，当所有连接结束的时候，server 正式关闭，并抛出 close 事件。
一般提供了callback，就不用监听close; 监听了close，就不用添加callback。

#### 其他server.listen()

其实除了 server.listen(PORT) 这种监听方式外，还有以下几种相对不那么常用的监听方式。用到的时候看看文档就行了。

server.listen(handle[, callback])：监听本地文件描述符（fd）（windows不支持），或者server，或者socket
server.listen(path[, callback])：监听本地socket，创建一个 UNIX socket server 。
server.listen([port][, hostname][, backlog][, callback])

#### 网络超时 server.setTimeout(msecs, callback)

设置网络连接的超时时间。当超过 msecs 没有响应时，网络就会自动断开。

如果传了 callback，那么当 timeout 发生时，就会将timeout的socket作为参数传给callback。

注意，一般情况下超时的socket会自动销毁。但当你传了callback后，你就需要手动end或者destroy这个socket。

### 不常用属性

server.listening：是否在监听连接
server.timeout：设置超时时间（毫秒），注意，修改这个值，只会对新建立的连接产生影响。此外，将timeout设置为0，就会禁用自动超时行为。（目测不推荐）
server.maxHeadersCount：客户端最多传送的header数量，默认是1000，如果设置为0，则没有限制。（问题：如果超过1000怎么办？？）


## 11 网络地址解析 url

### 模块概述

nodejs中，提供了**url**这个非常实用的模块，用来做URL的解析。在做node服务端的开发时会经常用到。使用很简单，总共只有3个方法。

正式讲解前，各位同学先把下面这个图记在心上（来自nodejs官网），先对URL有一个直观的认识。

![](/assets/url.png)

### 模块方法概述

url模块三个方法分别是：

* **.parse\(urlString\)**：将url字符串，解析成object，便于开发者进行操作。
* **.format\(urlObj\)**：.parse\(\) 方法的反向操作。
* **.resove\(from, to\)**：以from作为起始地址，解析出完整的目标地址（还是看直接看例子好些）

### url解析：url.parse\(\)

> 完整语法：url.parse\(urlString\[, parseQueryString\[, slashesDenoteHost\]\]\)

使用比较简单，几个要点备忘如下。

1. **parseQueryString**：（默认为false）如为false，则`urlObject.query`为未解析的字符串，比如`nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1`，且对应的值不会decode；如果`parseQueryString`为true，则`urlObject.query`为object，比如`{ nick: '程序猿小卡' }`，且值会被decode；
2. **slashesDenoteHos**：（默认为false）如果为true，那么类似`//foo/bar`里的`foo`就会被认为是`hostname`；如果为false，则`foo`被认为是pathname的一部分。
3. 关于解析得到的 urlObject ，会在下一小节进行详细介绍。

#### 例子1：参数值不进行解析

代码如下：

```javascript
var url = require('url');
var str = 'http://Chyingp:HelloWorld@ke.qq.com:8080/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1#part=1';

var obj = url.parse(str);
console.log(obj);
```

输出如下：

```javascript
Url {
  protocol: 'http:',
  slashes: true,
  auth: 'Chyingp:HelloWorld',
  host: 'ke.qq.com:8080',
  port: '8080',
  hostname: 'ke.qq.com',
  hash: '#part=1',
  search: '?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  query: 'nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  pathname: '/index.html',
  path: '/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  href: 'http://Chyingp:HelloWorld@ke.qq.com:8080/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1#part=1' }
```

#### 例子2：对参数值进行decode

代码如下：

```javascript
var url = require('url');
var str = 'http://Chyingp:HelloWorld@ke.qq.com:8080/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1#part=1';

var obj = url.parse(str, true);
console.log(obj);
```

输出如下，对比上面的例子会发现，**query** 字段被解析成了object，并且decode过。

```bash
Url {
  protocol: 'http:',
  slashes: true,
  auth: 'Chyingp:HelloWorld',
  host: 'ke.qq.com:8080',
  port: '8080',
  hostname: 'ke.qq.com',
  hash: '#part=1',
  search: '?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  query: { nick: '程序猿小卡' },
  pathname: '/index.html',
  path: '/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  href: 'http://Chyingp:HelloWorld@ke.qq.com:8080/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1#part=1' }
```

#### 例子3：针对路径 \/\/foo\/bar 的处理

代码如下：

```
var url = require('url');
var str = '//foo/bar';

var obj = url.parse(str, true, false);
console.log(obj);

obj = url.parse(str, true, true);
console.log(obj);
```

输出如下，自行对比两者之间的差异：

```bash
Url {
  protocol: null,
  slashes: null,
  auth: null,
  host: null,
  port: null,
  hostname: null,
  hash: null,
  search: '',
  query: {},
  pathname: '//foo/bar',
  path: '//foo/bar',
  href: '//foo/bar' }
Url {
  protocol: null,
  slashes: true,
  auth: null,
  host: 'foo',
  port: null,
  hostname: 'foo',
  hash: null,
  search: '',
  query: {},
  pathname: '/bar',
  path: '/bar',
  href: '//foo/bar' }
```

### 关于urlObject

以上面的作为例子，粗略讲解下`urlObject`。更多细节可参考[官方文档](https://nodejs.org/api/url.html#url_url_strings_and_url_objects)。

* protocol：协议，需要注意的是包含了`:`，并且是小写的。
* slashes：如果`:`后面跟了两个`//`，那么为true。
* auth：认证信息，如果有密码，为`usrname:passwd`，如果没有，则为`usrname`。注意，这里区分大小写。
* host：主机名。注意包含了端口，比如`ke.qq.com:8080`，并且是小写的。
* hostname：主机名，不包含端口，并且是小写的。
* hash：哈希部分，注意包含了`#`。
* search：查询字符串，注意，包含了`?`，此外，值是没有经过decode的。
* query：字符串 或者 对象。如果是字符串，则是`search`去掉`?`，其余一样；如果是对象，那么是decode过的。
* path：路径部分，包含search部分。
* pathname：路径部分，不包含search部分。
* href：原始的地址。不过需要注意的是，`protocol`、`host`会被转成小写字母。

```javascript
{
  protocol: 'http:',
  slashes: true,
  auth: 'Chyingp:HelloWorld',
  host: 'ke.qq.com:8080',
  port: '8080',
  hostname: 'ke.qq.com',
  hash: '#part=1',
  search: '?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  query: { nick: '程序猿小卡' },
  pathname: '/index.html',
  path: '/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1',
  href: 'http://Chyingp:HelloWorld@ke.qq.com:8080/index.html?nick=%E7%A8%8B%E5%BA%8F%E7%8C%BF%E5%B0%8F%E5%8D%A1#part=1' }
```

### url拼接：url.format\(urlObject\)

> 完整语法：url.format\(urlObject\)

`url.parse(str)`的反向操作，没什么好说的。`urlObject`包含了很多字段，比如`protocol`、`slashes`、`protocol`等，且不一定需要全部传，所以有一套解析逻辑。

过程比较冗长，大部分时候不需要用到，直接贴[官方文档](https://nodejs.org/api/url.html#url_url_format_urlobject)的链接，有需要再看。

### url.resolve\(from, to\)

用法比较简单，直接贴官方文档的例子

```javascript
url.resolve('/one/two/three', 'four')         // '/one/two/four'
url.resolve('http://example.com/', '/one')    // 'http://example.com/one'
url.resolve('http://example.com/one', '/two') // 'http://example.com/two'
```

## 12 URL查询字符串 querystring

### 模块概述

在nodejs中，提供了**querystring**这个模块，用来做url查询参数的解析，使用非常简单。

模块总共有四个方法，绝大部分时，我们只会用到 **.parse()**、 **.stringify()**两个方法。剩余的方法，感兴趣的同学可自行查看文档。

* **.parse()**：对url查询参数（字符串）进行解析，生成易于分析的json格式。
* **.stringif()**：跟**.parse()**相反，用于拼接查询查询。

```javascript
querystring.parse(str[, sep[, eq[, options]]])
querystring.stringify(obj[, sep[, eq[, options]]])
```

### 查询参数解析：querystring.parse()

>参数：querystring.parse(str[, sep[, eq[, options]]])

第四个参数几乎不会用到,直接不讨论. 第二个, 第三个其实也很少用到,但某些时候还是可以用一下。直接看例子

```javascript
var querystring = require('querystring');
var str = 'nick=casper&age=24';
var obj = querystring.parse(str);

```

输出如下

```javascript
{
    "nick": "casper",
    "age": "24"
}
```

再来看下`sep`、`eq`有什么作用。相当于可以替换`&`、`=`为自定义字符，对于下面的场景来说还是挺省事的。

```javascript
var str1 = 'nick=casper&age=24&extra=name-chyingp|country-cn';
var obj1 = querystring.parse(str1);
var obj2 = querystring.parse(obj1.extra, '|', '-');
console.log(JSON.stringify(obj2, null, 4));
```

输出如下

```javascript
{
    "name": "chyingp",
    "country": "cn"
}
```

### 查询参数拼接：querystring.stringify()

>querystring.stringify(obj[, sep[, eq[, options]]])

没什么好说的，相当于`parse`的逆向操作。直接看代码

```javascript
var querystring = require('querystring');

var obj1 = {
    "nick": "casper",
    "age": "24"
};
var str1 = querystring.stringify(obj1);
console.log(str1);

var obj2 = {
    "name": "chyingp",
    "country": "cn"
};
var str2 = querystring.stringify(obj2, '|', '-');
console.log(str2);
```

输出如下

```javascript
nick=casper&age=24
name-chyingp|country-cn
```

### 相关链接

官方文档：https://nodejs.org/api/querystring.html


## 13 本地路径处理 path

### 模块概览

在nodejs中，path是个使用频率很高，但却让人又爱又恨的模块。部分因为文档说的不够清晰，部分因为接口的平台差异性。

将path的接口按照用途归类，仔细琢磨琢磨，也就没那么费解了。

### 获取路径/文件名/扩展名

* 获取路径：path.dirname(filepath)
* 获取文件名：path.basename(filepath)
* 获取扩展名：path.extname(filepath)

#### 获取所在路径

例子如下：

```javascript
var path = require('path');
var filepath = '/tmp/demo/js/test.js';

// 输出：/tmp/demo/js
console.log( path.dirname(filepath) );
```

#### 获取文件名

严格意义上来说，path.basename(filepath) 只是输出路径的最后一部分，并不会判断是否文件名。

但大部分时候，我们可以用它来作为简易的“获取文件名“的方法。

```javascript
var path = require('path');

// 输出：test.js
console.log( path.basename('/tmp/demo/js/test.js') );

// 输出：test
console.log( path.basename('/tmp/demo/js/test/') );

// 输出：test
console.log( path.basename('/tmp/demo/js/test') );
```

如果只想获取文件名，单不包括文件扩展呢？可以用上第二个参数。

```javascript
// 输出：test
console.log( path.basename('/tmp/demo/js/test.js', '.js') );
```

#### 获取文件扩展名

简单的例子如下：

```javascript
var path = require('path');
var filepath = '/tmp/demo/js/test.js';

// 输出：.js
console.log( path.extname(filepath) );
```

更详细的规则是如下：（假设 path.basename(filepath) === B ）

* 从B的最后一个`.`开始截取，直到最后一个字符。
* 如果B中不存在`.`，或者B的第一个字符就是`.`，那么返回空字符串。

直接看[官方文档](https://nodejs.org/api/path.html#path_path_extname_path)的例子

```javascript
path.extname('index.html')
// returns '.html'

path.extname('index.coffee.md')
// returns '.md'

path.extname('index.')
// returns '.'

path.extname('index')
// returns ''

path.extname('.index')
// returns ''

```
### 规范化
#### path.normalize(p)
规范化路径，处理冗余的“..”、“.”、“/”字符。发现多个斜杠时，会替换成一个斜杠。当路径末尾包含一个斜杠时，保留。Windows系统使用反斜杠　
```
var path = require('path');
console.log(path.normalize('a/b/c/../user/bin'));// a\b\user\bin
console.log(path.normalize('a/b/c///../user/bin/'));// a\b\user\bin\
console.log(path.normalize('a/b/c/../../user/bin'));// a\user\bin
console.log(path.normalize('a/b/c/.././///../user/bin/..'));// a\user
console.log(path.normalize('a/b/c/../../user/bin/../../'));// a\
console.log(path.normalize('a/../../user/bin/../../'));//..\
console.log(path.normalize('a/../../user/bin/../../../../'));// ..\..\..\
console.log(path.normalize('./a/.././user/bin/./'));//user\bin\
```
#### path.join([path1], [path2], [...])
将多个路径结合在一起，并转换为规范化路径
```
var path = require('path');
console.log(path.join('////./a', 'b////c', 'user/'));//\a\b\c\user
console.log(path.join('a', '../../', 'user/'));//..\user\
```

#### path.resolve([from ...], to)
从源地址 from 到目的地址 to 的绝对路径，类似在shell里执行一系列的cd命令
```
path.resolve('foo/bar', '/tmp/file/', '..', 'a/../subfile')
```
类似于:
```
cd foo/bar
cd /tmp/file/
cd ..
cd a/../subfile
pwd
```
如果某个from或to参数是绝对路径（比如 'E:/abc'，或是以“/”开头的路径），则将忽略之前的from参数
```
var path = require('path');
console.log(path.resolve('.', 'testFiles/..', 'trdLayer'));//D:\project\trdLayer
console.log(path.resolve('..', 'testFiles', 'a.txt'));//D:\testFiles\a.txt
console.log(path.resolve('D:', 'abc', 'D:/a'));//D:\a
console.log(path.resolve('abc', 'ok.gif'));//D:\project\abc\ok.gif
console.log(path.resolve('abc', '..', 'a/../subfile')); //D:\project\subfile
```

#### path.resolve([...paths])

这个接口的说明有点啰嗦。你可以想象现在你在shell下面，从左到右运行一遍`cd path`命令，最终获取的绝对路径/文件名，就是这个接口所返回的结果了。

比如 `path.resolve('/foo/bar', './baz')` 可以看成下面命令的结果

```bash
cd /foo/bar
cd ./baz
```

更多对比例子如下：

```javascript
var path = require('path');

// 假设当前工作路径是 /Users/a/Documents/git-code/nodejs-learning-guide/examples/2016.11.08-node-path

// 输出 /Users/a/Documents/git-code/nodejs-learning-guide/examples/2016.11.08-node-path
console.log( path.resolve('') )

// 输出 /Users/a/Documents/git-code/nodejs-learning-guide/examples/2016.11.08-node-path
console.log( path.resolve('.') )

// 输出 /foo/bar/baz
console.log( path.resolve('/foo/bar', './baz') );

// 输出 /foo/bar/baz
console.log( path.resolve('/foo/bar', './baz/') );

// 输出 /tmp/file
console.log( path.resolve('/foo/bar', '/tmp/file/') );

// 输出 /Users/a/Documents/git-code/nodejs-learning-guide/examples/2016.11.08-node-path/www/js/mod.js
console.log( path.resolve('www', 'js/upload', '../mod.js') );

```

### 路径解析

#### path.normalize(filepath)

从官方文档的描述来看，path.normalize(filepath) 应该是比较简单的一个API，不过用起来总是觉得没底。

为什么呢？API说明过于简略了，包括如下：

* 如果路径为空，返回`.`，相当于当前的工作路径。
* 将对路径中重复的路径分隔符（比如linux下的`/`)合并为一个。
* 对路径中的`.`、`..`进行处理。（类似于shell里的`cd ..`）
* 如果路径最后有`/`，那么保留该`/`。

感觉stackoverflow上一个兄弟对这个API的解释更实在，[原文链接](http://stackoverflow.com/questions/10822574/difference-between-path-normalize-and-path-resolve-in-node-js)。

>In other words, path.normalize is "What is the shortest path I can take that will take me to the same place as the input"


代码示例如下。建议读者把代码拷贝出来运行下，看下实际效果。

```javascript
var path = require('path');
var filepath = '/tmp/demo/js/test.js';

var index = 0;

var compare = function(desc, callback){
  console.log('[用例%d]：%s', ++index, desc);
  callback();
  console.log('\n');
};

compare('路径为空', function(){
  // 输出 .
  console.log( path.normalize('') );
});

compare('路径结尾是否带/', function(){
  // 输出 /tmp/demo/js/upload
  console.log( path.normalize('/tmp/demo/js/upload') );

  // /tmp/demo/js/upload/
  console.log( path.normalize('/tmp/demo/js/upload/') );
});

compare('重复的/', function(){
  // 输出 /tmp/demo/js
  console.log( path.normalize('/tmp/demo//js') );
});

compare('路径带..', function(){
  // 输出 /tmp/demo/js
  console.log( path.normalize('/tmp/demo/js/upload/..') );
});

compare('相对路径', function(){
  // 输出 demo/js/upload/
  console.log( path.normalize('./demo/js/upload/') );

  // 输出 demo/js/upload/
  console.log( path.normalize('demo/js/upload/') );
});

compare('不常用边界', function(){
  // 输出 ..
  console.log( path.normalize('./..') );

  // 输出 ..
  console.log( path.normalize('..') );

  // 输出 ../
  console.log( path.normalize('../') );

  // 输出 /
  console.log( path.normalize('/../') );
  
  // 输出 /
  console.log( path.normalize('/..') );
});
```

感兴趣的可以看下 path.normalize(filepath) 的node源码如下：[传送门](https://github.com/nodejs/node/blob/master/lib/path.js)

### 文件路径分解/组合

* path.format(pathObject)：将pathObject的root、dir、base、name、ext属性，按照一定的规则，组合成一个文件路径。
* path.parse(filepath)：path.format()方法的反向操作。

我们先来看看官网对相关属性的说明。

首先是linux下

```bash
┌─────────────────────┬────────────┐
│          dir        │    base    │
├──────┬              ├──────┬─────┤
│ root │              │ name │ ext │
"  /    home/user/dir / file  .txt "
└──────┴──────────────┴──────┴─────┘
(all spaces in the "" line should be ignored -- they are purely for formatting)
```


然后是windows下

```bash
┌─────────────────────┬────────────┐
│          dir        │    base    │
├──────┬              ├──────┬─────┤
│ root │              │ name │ ext │
" C:\      path\dir   \ file  .txt "
└──────┴──────────────┴──────┴─────┘
(all spaces in the "" line should be ignored -- they are purely for formatting)
```

#### path.format(pathObject)

阅读相关API文档说明后发现，path.format(pathObject)中，pathObject的配置属性是可以进一步精简的。

根据接口的描述来看，以下两者是等价的。

* `root` vs `dir`：两者可以互相替换，区别在于，路径拼接时，`root`后不会自动加`/`，而`dir`会。
* `base` vs `name+ext`：两者可以互相替换。

```javascript
var path = require('path');

var p1 = path.format({
  root: '/tmp/', 
  base: 'hello.js'
});
console.log( p1 ); // 输出 /tmp/hello.js

var p2 = path.format({
  dir: '/tmp', 
  name: 'hello',
  ext: '.js'
});
console.log( p2 );  // 输出 /tmp/hello.js
```

#### path.parse(filepath)

path.format(pathObject) 的反向操作，直接上官网例子。

四个属性，对于使用者是挺便利的，不过path.format(pathObject) 中也是四个配置属性，就有点容易搞混。

```javascript
path.parse('/home/user/dir/file.txt')
// returns
// {
//    root : "/",
//    dir : "/home/user/dir",
//    base : "file.txt",
//    ext : ".txt",
//    name : "file"
// }
```

### 获取相对路径

接口：path.relative(from, to)

描述：从`from`路径，到`to`路径的相对路径。

边界：

* 如果`from`、`to`指向同个路径，那么，返回空字符串。
* 如果`from`、`to`中任一者为空，那么，返回当前工作路径。

上例子：

```javascript
var path = require('path');

var p1 = path.relative('/data/orandea/test/aaa', '/data/orandea/impl/bbb');
console.log(p1);  // 输出 "../../impl/bbb"

var p2 = path.relative('/data/demo', '/data/demo');
console.log(p2);  // 输出 ""

var p3 = path.relative('/data/demo', '');
console.log(p3);  // 输出 "../../Users/a/Documents/git-code/nodejs-learning-guide/examples/2016.11.08-node-path"
```


### 平台相关接口/属性

以下属性、接口，都跟平台的具体实现相关。也就是说，同样的属性、接口，在不同平台上的表现不同。

* path.posix：path相关属性、接口的linux实现。
* path.win32：path相关属性、接口的win32实现。
* path.sep：路径分隔符。在linux上是`/`，在windows上是`\`。
* path.delimiter：path设置的分割符。linux上是`:`，windows上是`;`。

注意，当使用 path.win32 相关接口时，参数同样可以使用`/`做分隔符，但接口返回值的分割符只会是`\`。

直接来例子更直观。

```bash
> path.win32.join('/tmp', 'fuck')
'\\tmp\\fuck'
> path.win32.sep
'\\'
> path.win32.join('\tmp', 'demo')
'\\tmp\\demo'
> path.win32.join('/tmp', 'demo')
'\\tmp\\demo'
```

#### path.delimiter

linux系统例子：

```bash
console.log(process.env.PATH)
// '/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin'

process.env.PATH.split(path.delimiter)
// returns ['/usr/bin', '/bin', '/usr/sbin', '/sbin', '/usr/local/bin']
```

windows系统例子：

```bash
console.log(process.env.PATH)
// 'C:\Windows\system32;C:\Windows;C:\Program Files\node\'

process.env.PATH.split(path.delimiter)
// returns ['C:\\Windows\\system32', 'C:\\Windows', 'C:\\Program Files\\node\\']
```

## 14 事件机制 events

### 模块概览

events模块是node的核心模块之一，几乎所有常用的node模块都继承了events模块，比如http、fs等。

模块本身非常简单，API虽然也不少，但常用的就那么几个，这里举几个简单例子。

### 基础例子

下面一共是6个例子，都非常简单，可以直接拷贝出来运行。例子5比较有意思，虽然也并不复杂，但确实是容易记错的点，感兴趣的同学可以看下。

#### 例子1：单个事件监听器

```js
var EventEmitter = require('events');

class Man extends EventEmitter {}

var man = new Man();

man.on('wakeup', function(){
    console.log('man has woken up');
});

man.emit('wakeup');
// 输出如下：
// man has woken up
```

#### 例子2：同个事件，多个事件监听器

可以看到，事件触发时，事件监听器按照注册的顺序执行。

```js
var EventEmitter = require('events');

class Man extends EventEmitter {}

var man = new Man();

man.on('wakeup', function(){
    console.log('man has woken up');
});

man.on('wakeup', function(){
    console.log('man has woken up again');
});

man.emit('wakeup');

// 输出如下：
// man has woken up
// man has woken up again
```

#### 例子3：只运行一次的事件监听器

```js
var EventEmitter = require('events');

class Man extends EventEmitter {}

var man = new Man();

man.on('wakeup', function(){
    console.log('man has woken up');
});

man.once('wakeup', function(){
    console.log('man has woken up again');
});

man.emit('wakeup');
man.emit('wakeup');

// 输出如下：
// man has woken up
// man has woken up again
// man has woken up
```

#### 例子4：注册事件监听器前，事件先触发

可以看到，注册事件监听器前，事件先触发，则该事件会直接被忽略。

```js
var EventEmitter = require('events');

class Man extends EventEmitter {}

var man = new Man();

man.emit('wakeup', 1);

man.on('wakeup', function(index){
    console.log('man has woken up ->' + index);
});

man.emit('wakeup', 2);
// 输出如下：
// man has woken up ->2
```

#### 例子5：异步执行，还是顺序执行

例子很简单，但非常重要。究竟是代码1先执行，还是代码2先执行，这点差异，无论对于我们理解别人的代码，还是自己编写node程序，都非常关键。

实践证明，代码1先执行了。(node v6.1.0)

```js
var EventEmitter = require('events');

class Man extends EventEmitter {}

var man = new Man();

man.on('wakeup', function(){
    console.log('man has woken up'); // 代码1
});

man.emit('wakeup');

console.log('woman has woken up');  // 代码2

// 输出如下：
// man has woken up
// woman has woken up
```

#### 例子6：移除事件监听器

```js
var EventEmitter = require('events');

function wakeup(){
    console.log('man has woken up');
}

class Man extends EventEmitter {}

var man = new Man();

man.on('wakeup', wakeup);
man.emit('wakeup');

man.removeListener('wakeup', wakeup);
man.emit('wakeup');

// 输出如下：
// man has woken up
```

### 相关链接

https://nodejs.org/api/events.html


## 15 流操作 stream

### 模块概览

nodejs的核心模块，基本上都是stream的的实例，比如process.stdout、http.clientRequest。

对于大部分的nodejs开发者来说，平常并不会直接用到stream模块，只需要了解stream的运行机制即可（非常重要）。

而对于想要实现自定义stream实例的开发者来说，就得好好研究stream的扩展API了，比如gulp的内部实现就大量用到了自定义的stream类型。

来个简单的例子镇楼，几行代码就实现了读取文件内容，并打印到控制台：

```js
const fs = require('fs');

fs.createReadStream('./sample.txt').pipe(process.stdout);
```

### Stream分类

在nodejs中，有四种stream类型：

* Readable：用来读取数据，比如 `fs.createReadStream()`。
* Writable：用来写数据，比如 `fs.createWriteStream()`。
* Duplex：可读+可写，比如 `net.Socket()`。
* Transform：在读写的过程中，可以对数据进行修改，比如 `zlib.createDeflate()`（数据压缩/解压）。

### Readable Stream

以下都是nodejs中常见的Readable Stream，当然还有其他的，可自行查看文档。

* http.IncomingRequest
* fs.createReadStream()
* process.stdin
* 其他

例子一：

```js
var fs = require('fs');

fs.readFile('./sample.txt', 'utf8', function(err, content){
	// 文件读取完成，文件内容是 [你好，我是程序猿小卡]
	console.log('文件读取完成，文件内容是 [%s]', content);
});
```

例子二：

```js
var fs = require('fs');

var readStream = fs.createReadStream('./sample.txt');
var content = '';

readStream.setEncoding('utf8');

readStream.on('data', function(chunk){
	content += chunk;
});

readStream.on('end', function(chunk){
	// 文件读取完成，文件内容是 [你好，我是程序猿小卡]
	console.log('文件读取完成，文件内容是 [%s]', content);
});
```

例子三：

这里使用了`.pipe(dest)`，好处在于，如果文件

```js
var fs = require('fs');

fs.createReadStream('./sample.txt').pipe(process.stdout);
```

注意：这里只是原封不动的将内容输出到控制台，所以实际上跟前两个例子有细微差异。可以稍做修改，达到上面同样的效果

```js
var fs = require('fs');

var onEnd = function(){
	process.stdout.write(']');	
};

var fileStream = fs.createReadStream('./sample.txt');
fileStream.on('end', onEnd)

fileStream.pipe(process.stdout);

process.stdout.write('文件读取完成，文件内容是[');

// 文件读取完成，文件内容是[你好，我是程序猿小卡]
```

### Writable Stream

同样以写文件为例子，比如想将`hello world`写到`sample.txt`里。

例子一：

```js
var fs = require('fs');
var content = 'hello world';
var filepath = './sample.txt';

fs.writeFile(filepath, content);
```

例子二：

```js
var fs = require('fs');
var content = 'hello world';
var filepath = './sample.txt';

var writeStram = fs.createWriteStream(filepath);
writeStram.write(content);
writeStram.end();
```

### Duplex Stream

最常见的Duplex stream应该就是`net.Socket`实例了，在前面的文章里有接触过，这里就直接上代码了，这里包含服务端代码、客户端代码。

服务端代码：

```js
var net = require('net');
var opt = {
	host: '127.0.0.1',
	port: '3000'
};

var client = net.connect(opt, function(){
	client.write('msg from client');  // 可写
});

// 可读
client.on('data', function(data){
    // server: msg from client [msg from client]
	console.log('client: got reply from server [%s]', data);
	client.end();
});
```

客户端代码：

```js
var net = require('net');
var opt = {
	host: '127.0.0.1',
	port: '3000'
};

var client = net.connect(opt, function(){
	client.write('msg from client');  // 可写
});

// 可读
client.on('data', function(data){
    // lient: got reply from server [reply from server]
	console.log('client: got reply from server [%s]', data);
	client.end();
});
```

### Transform Stream

Transform stream是Duplex stream的特例，也就是说，Transform stream也同时可读可写。跟Duplex stream的区别点在于，Transform stream的输出与输入是存在相关性的。

常见的Transform stream包括`zlib`、`crypto`，这里举个简单例子：文件的gzip压缩。

```js
var fs = require('fs');
var zlib = require('zlib');

var gzip = zlib.createGzip();

var inFile = fs.createReadStream('./extra/fileForCompress.txt');
var out = fs.createWriteStream('./extra/fileForCompress.txt.gz');

inFile.pipe(gzip).pipe(out);
```

### 相关链接

https://nodejs.org/api/stream.html
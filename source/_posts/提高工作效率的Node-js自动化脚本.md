---
title: 日常提高工作效率的Node.js自动化脚本
date: 2020-04-03 11:48:52
tags:
- nodejs
- 自动化
- 脚本
- 案例
categories:
- [nodejs]
- [案例]
---
## 写在前面
真正的大牛都是习惯于使用脚本来实现自动化操作。随着 Node.js 的出现，前端开发工程师也可以通过 Node.js 来编写一些日常的工作脚本，从而减少手工操作，提高工作效率，从而节省出更多的时间花在核心的技术学习上。也使得我们的工作时刻保持新鲜。在这我会和大家分享我日常工作中的 Node.js 脚本。


- 自动备份
- 自动执行命令
- 自动修改文件内容
- 文件的压缩与解压
- 服务器文件上传下载
- 邮件发送
- 处理 Excel

<!-- more -->

## 环境搭建
使用 Node.js 搭建环境超级简单。只需要下载 Node.js 即可。自己电脑再下载一个好用的编辑器即可。这里推荐使用 VS Cod` 编辑器。无需 Java 那样配置环境、安装 JDK，还有各种工作区等，因为我只是想写个脚本而已。

安装好 Node.js 之后，新建一个文件夹 node-script 来存放所有的脚本，在命令提示符下进入该目录，执行以下命令快速生成 package.json：

``` js
npm init -y
```

下面开始编写 Node.js 脚本。

## 代码备份脚本

在 node-script 文件夹下新建文件 bak.js：
``` js
### node-script项目结构：
─node-script
  └─node_modules
  └─bak.js
  └─package.json
```
代码备份无非就是将文件夹自动存放于电脑某个文件夹下，代码备份一般来说可以有两种：

- 需要备份时手动执行一次，一般用于代码需要大改前保留一份原有数据
- 每天定时备份一次，例如一些重要的数据

代码备份需要使用 Node.js 的 fs 模块来实现。但是我们使用 fs 模块来写代码量会很大，而且一点都不简洁。很多的开源库和 CLI 工具都使用了第三方模块 [ShellJS](https://www.npmjs.com/package/shelljs)。

ShellJS 简单来说就是，可以执行它提供的方法来实现文件增删改查、复制、执行系统命令等操作。官网示例：

``` js
var shell = require('shelljs');

if (!shell.which('git')) {
  shell.echo('Sorry, this script requires git');
  shell.exit(1);
}

// Copy files to release dir
shell.rm('-rf', 'out/Release');
shell.cp('-R', 'stuff/', 'out/Release');

// Replace macros in each .js file
shell.cd('lib');
shell.ls('*.js').forEach(function (file) {
  shell.sed('-i', 'BUILD_VERSION', 'v0.1.2', file);
  shell.sed('-i', /^.*REMOVE_THIS_LINE.*$/, '', file);
  shell.sed('-i', /.*REPLACE_LINE_WITH_MACRO.*\n/, shell.cat('macro.js'), file);
});
shell.cd('..');

// Run external tool synchronously
if (shell.exec('git commit -am "Auto-commit"').code !== 0) {
  shell.echo('Error: Git commit failed');
  shell.exit(1);
}
```

使用 ShellJS 前需要安装它：

``` js
npm i shelljs -D
```

**文件夹备份代码如下。**
bak.js
``` js
const shell = require("shelljs")
const moment = require("moment")
const current = moment().format("YYYYMMDDhhmmss") // 20191101101426格式
const folder = `src_${current}`
// 先创建一个时间信息的文件夹，防止覆盖同一个文件夹
shell.mkdir('-p',"./bak/"+folder)
// shelljs 复制文件或者文件夹方法。-rf表示强制和递归方式复制。
shell.cp("-rf",'./test','./bak/'+folder)
```
上述代码主要使用了 ShellJS 提供的新建文件夹方法 mkdir 和复制方法 cp 来实现。还用到了 JavaScript 中一个著名的处理时间模块 moment 来实现。

每次执行上述代码都需要使用命令 node bak.js 来执行这个脚本。当然我们也可以使用 npm script 方式来执行。只需要在 package.json 中加入自定义的 script 字段：

``` js
"script":{
    "bak":"node ./bak.js"
}
```
执行脚本时只需要：
``` js
npm run bak
```
下面我将这个脚本升级一下，可以让其每天 0 时自动执行一次，即定时任务，这里我使用了 Node 定时任务的一个第三方模块 node-schedule。
``` js
npm i node-schedule --save
```
每天 0 时执行一次：
``` js
const schedule = require("node-schedule")
function copy(){
  const current = moment().format("YYYYMMDDhhmmss") // 20191101101426格式
  const folder = `src_${current}`
  shell.mkdir('-p',"./bak/"+folder)
  shell.cp("-rf",'./test','./bak/'+folder)
  shell.echo("备份完成！")
}
// 每天的0时执行回调函数
schedule.scheduleJob('00 00 00 * * *',()=>{
  copy()
}); 
```
schedule.scheduleJob() 函数第一个参数为通配符表示时间，一般有六个 * 号。从左到右边依次为秒、分、时、日期、月份、星期。
``` js
*  *  *  *  *  *
┬ ┬ ┬ ┬ ┬ ┬
│ │ │ │ │ |
│ │ │ │ │ └ day of week (0 - 7) (0 or 7 is Sun)
│ │ │ │ └───── month (1 - 12)
│ │ │ └────────── day of month (1 - 31)
│ │ └─────────────── hour (0 - 23)
│ └──────────────────── minute (0 - 59)
└───────────────────────── second (0 - 59, OPTIONAL)
每分钟的第 01 秒触发： '01 * * * * *'
每小时的 1 分 01 秒触发 ：'01 1 * * * *'
每天的凌晨 1点1分30秒触发 ：'30 1 1 * * *'
每月的 1 日1点1分30秒触发 ：'30 1 1 1 * *'
2019 年的 1 月 1 日1 点 1 分30秒触发 ：'30 1 1 1 2019 *'
每周1的1点1分30秒触发 ：'30 1 1 * * 1'
```

## 自动执行命令
使用 ShellJS 的 exec 方法可以执行程序命令，例如:
``` js
const shell = require("shelljs")
shell.exec("cls") // 执行windows下的清空显示命令
shell.exec("npm run dev") // 执行npm script命令
```
一般来说在执行命令时可能一个命令执行完毕后可能会退出 Node 程序，例如下面情形：
``` js
const shell = require("shelljs")
shell.exec(""npm run build)
shell.cp("-rf","./dist","../resource")
```
上面的 npm run build 被执行完毕后可能会退出程序，使得 cp 命令不被执行。其实 ShellJS 提供的 exec() 方法还可以接收参数和回调函数：
``` js
const shell = require("shelljs")

shell.exec('node --version', {
  silent:true, // 执行过程中命令提示符不会显示过程信息
  async:false  // 是否异步执行
},(code, stdout, stderr)=>{
 // 执行完毕后继续执行这个回调函数 code表示状态码，0成功，1失败 。stdout表示上一步输入
});
```
将下一步操作放在回调函数内就可以使得程序继续执行下去了。

## 自动修改文件内容
在进行复制代码包的过程中，总少不了各种文件参数的修改。例如程序包在复制到服务器上去之后需要修改里面的一个版本文件 version.js。每替换一次版本号需要自动加 1：
``` js
version = "v1.1.0"
```
利用 ShellJS 的 sed 方法可以很方便地实现文件内容替换。

**sed([options,] search_regex, replacement, file [, file …])**

``` js
// 替换文件内容，-i表示不创建备份，第二个参数为需要替换的内容，第三个参数为替换成的新内容，最后一个参数为文件路径
shell.sed("-i","version","aa","./version.txt") // 将version 替换成 aa
// 正则表达式方式,将version-替换成v-
shell.sed("-i",/version\s=/,"v =","./version.txt")
```
版本最后一位每次打包时自动加 1：
``` js
const shell = require("shelljs")
const content = shell.cat('./version.txt')
const rep = content.stdout.split('.')
rep[rep.length-1] = Number(rep[rep.length-1])+1
shell.sed("-i",content,rep.join('.'),"./version.txt")
```
如果需要实现复杂的字符串替换，可以使用正则表达式的方式。

## 接收控制台输入
有时候执行脚本时需要和使用者进行交互和对话，用户输入 YES 或者 NO 来执行不同代码分支，例如上述的自动变更版本号的脚本，有时候并不想每次打包都更新版本（例如一个小小的改动而已），实现接收控制台输入可以使用 node 的 process 模块。

``` js
process.stdout.write("需要变更版本号吗(y/n)？");
process.stdin.on('data',(input)=>{
  input = input.toString().trim();
  if (['Y', 'y', 'YES', 'yes'].indexOf(input) > -1){
    // 选择了yes Y时执行这里
  };
  if (['N', 'n', 'NO', 'no'].indexOf(input) > -1) {
    process.exit(0); // 退出
  }
})
```

## 文件的压缩与解压

很多时候程序包上传到服务器上去很少是文件夹的方式，而是先将其压缩成特定的格式，再上传到服务器，上传之后再执行解压操作。一般来说压缩格式主要有 ZIP 和 GZIP 两种格式。这里我目前使用的 Node 文件压缩与解压的库是[compressing](https://github.com/node-modules/compressing)

使用 compressing 可以支持文件压缩、文件夹压缩，并且可以压缩成 ZIP、GZ 等多种格式。按照惯例，使用它需要手动安装一下：

``` js
npm i compressing --save
```

压缩 dist 文件夹成 ZIP 格式例子：

``` js
onst compressing = require("compressing")
compressing.zip.compressDir(resolve("dist/"), resolve(zipName)).then(() => {
  console.log("压缩成功！");
}).catch(err => {
  console.log("压缩失败");
  console.error(err);
});
```

压缩文件官方例子：

``` js
const compressing = require('compressing');

// compress a file 压缩一个文件
compressing.gzip.compressFile('file/path/to/compress', 'path/to/destination.gz')
.then(compressDone)
.catch(handleError);

// compress a file buffer 二进制方式
compressing.gzip.compressFile(buffer, 'path/to/destination.gz')
.then(compressDone)
.catch(handleError);

// compress a stream 使用流的方式
compressing.gzip.compressFile(stream, 'path/to/destination.gz')
.then(compressDone)
.catch(handleError);
```

## 服务器文件上传下载

处理好文件后，在很多时候往往还需要上传到服务器上实现自动部署，而不需要再使用一些 SFTP 工具来手动操作。我目前在实际工作中使用的是第三方模块 node-ssh，先进行安装：
``` js
npm i node-ssh --save
```
连接服务器执行服务器命令例子：
``` js
const node_ssh = require("node-ssh")
const ssh = new node_ssh()

ssh.connect({
    host:'182.11.18.14',
    username:'admin',
    password:'admin'
}).then(()=>{
    console.log("连接成功！")
    // 可以执行linux上的命令
    ssh.execCommand("node -v").then((res)=>{
        console.log(res.stdout)
    })
    // 将本地文件上传到服务器文件中。如果没有bak.js则会新建。
    ssh.putFile('./bak.js','/home/demo/bak.js').then((res)=>{
        console.log("上传成功！")
    },(err)=>{
        console.log(err)
    })
    // 多个文件上传方式 需要local和remote属性
    ssh.putFiles([
       { local:'./bak.js',remote:'/home/dist/bak.js'},
       { local:'./index.js',remote:'/home/dist/index.js'}
    ]).then(()=>{
        console.log("上传完成啦！")
    })
    // 文件夹上传
    ssh.putDirectory("./test",'/home/demo/test',{
      recursive: true, // 递归方式
      concurrency: 10,
    }).then((status)=>{
        console.log("文件夹上传成功！")
    })
}).catch((err)=>{
    console.log("连接失败！",err)
})
```

## 邮件发送

使用脚本来自动发送一些简单而重复的邮件信息显得很方便。例如一些监控系统，出现异常时自动触发发送邮件的 API，邮件还可以给用户推送一些实用的信息等。我们先安装第三方模块

``` js
npm install nodemailer --save
```

发送邮件程序：
``` js
const nodemailer =require('nodemailer')
var transporter = nodemailer.createTransport({
    service:'163', // 以163邮箱为例
    auth: {
      user:'123456@163.com',
      pass: 'admin' // 163邮箱授权密码
    }
})
var mailOptions = {
    from:'123456@163.com', // 发送邮件的邮箱
    to: '123456@qq.com',   // 给谁发送
    subject: '每日程序员新闻', // 右键主题
    text:'测试数据'  // 右键内容
}

function sendMail(){
    transporter.sendMail(mailOptions, function(error, info) {
        if (error) {
            console.log(error);
        } else {
            console.log('邮件发送成功！')
        }
    })
}
sendMail()
```
上面就是一个基本简单的邮件发送功能，我们还可以将其封装成一个模块。在需要时调用发送邮件接口即可。如果要发送带 HTML 格式的内容只需要在 mailOptions 对象中加入一个属性 html 即可。

当然也可以发送附件，只需要加入字段 attachments 即可。

``` js
var mailOptions = {
    from:'123456@163.com',
    to: '123456@qq.com',
    subject: '每日新闻',
    text:'1.this words was sent by mch',
    attachments:[
      {filename:'data.xlsx',path:'./data.xlsx'}
    ]
}
```
这里有两点要注意：

- attachemts 附件在 path 字段在条件允许下尽量使用英文名，不然可能会有难以意料错误；
- 进行邮件发送测试时最好不要随便和简单地填写一些无意义的文字，不然很多邮箱会检测出是垃圾邮件，导致发送失败！

## 处理 Excel
在 JavaScript 领域也有许多好用且流行的处理 Excel 的包，例如在 GitHub 上有着上万 Star 的 [sheetjs](https://sheetjs.com/)，虽然还有许多类似于 node-excel 这样的库，但是很多还是依赖了 SheetJS。下面我来介绍 SheetJS 的用法。
``` js
npm i xlsx --save
```
使用
``` JS
const XLSX = require("xlsx") // 纯前端也可以使用，使用import语法也可以
const wb = XLSX.readFile("./json.xlsx")
const sheets = wb.SheetNames // sheet数组
const worksheet = wb.Sheets[sheets[0]] // 第一张工作表对象
console.log(worksheet)
```
worksheet 返回一个 JSON 对象：
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200403122034.png)

单元格对象含义：

|-|-|
| -----  | :----- |
属性|	描述|
|w	|内容|
|t	|类型: b Boolean, n Number, e error, s String, d Date|
|f	|单元格样式|
|F	|排序样式|
|r	|富媒体编码|
|h	|HTML 绘制富媒体|
|c	|单元格描述|
|z	|样式个数|
|l	|单元格链接|
|s	|单元格主题|

例如现在要修改 demo.xlsx A1 单元格的内容为“hello,world”：
``` js
const XLSX = require("xlsx")
const wb = XLSX.readFile("./json.xlsx")
const sheets = wb.SheetNames // sheet数组
const worksheet = wb.Sheets[sheets[0]] // 第一张工作表对象
console.log(worksheet)
worksheet['A1']['v'] ="hello,world"
XLSX.writeFile(wb,'./demo.xlsx')
```
可以准备好一份 Excel 模版，每次操作就覆盖指定单元格的内容。这种方式很适合写工作周报。

也可以根据数据生成一份新的 Excel 文件（导出）：
``` js
const XLSX = require("xlsx")
const arr=[
    ["姓名","年龄",'性别'],
    ["jack","12",'男']
    ["rose","23",'女']
]
const wb = {
    SheetNames:['hello'],
    Sheets:{
        'hello':XLSX.utils.aoa_to_sheet(arr)
    }
}
XLSX.writeFile(wb,'test4.xlsx')
```







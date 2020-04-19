---
title: egret白鹭入门教程一
date: 2020-04-13 19:25:33
tags:
- 游戏开发
- canvas
categories:
- 游戏开发
---

## 什么是egret白鹭引擎
Egret是一套HTML5游戏开发解决方案，灵活地满足开发者开发 2D 或 3D 游戏的需求（渲染模式基于webgl与canvas切换,并且完善的跨平台支持,在任何平台都可使用

生态产品包含Egret Engine，Egret Wing，EgretVS，Res Depot，Texture Merger，TS Conversion，Egret Feather，Egret Inspector，DragonBones，Lakeshore等。

而核心产品是Egret Engine，是一个基于TypeScript语言开发的一个HTML5游戏引擎，其余的大多是开发和辅助工具。

## 环境准备
1、第一步：引擎库管理工具 EgretLauncher [下载地址](https://docs.egret.com/engine) 然后下一步...安装

2、第二步：打开 EgretLauncher，切换到引擎面板，并点击引擎的最新稳定版进行下载

3、第三步：切换到工具面板，可选择性的下载和安装开发工具（我没有安装，我用vscode进行开发）

<!-- more -->

**提供以下工具下载**
- Egret UI Editor
- Egret Wing 代码和界面制作工具
- DragonBones 龙骨动画制作工具
- TextureMerger 图片资源合并工具
- Res Depot 资源管理工具
- Egret Feather 粒子制作工具

注意：
> Egret UI Editor 是 Egret Wing 的升级版本，官方在 2019 年发布 Egret UI Editor 的设计目标是，使用 UI Editor + VSCode 取代现有的Egret Wing。

## 快速创建并启动项目

创建名为【HelloWorld】的一个项目
``` js
// 确认egret命令环境变量已经存在
egret create HelloWorld
```

启动HelloWorld项目

``` js
// 在编辑器打开项目，并执行命令
egret run HelloWorld
```
大功告成

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200413195420.png)

注意：
> 执行 `egret run HelloWorld -a` 可监控文件变化，实时编译最新结果

## 初步尝试
介绍项目目录：
- src是TypeScript源码目录，
- bin-debug是调试环境编译出来的js，
- libs是文件包的引用目录，
- template主要是app用到的runtime；
- resource是我们项目中的资源路径，一般我们把图片音频等素材放置asset目录下，json等配置文件放置config目录下，
- default.res.json是我们用于加载资源的json配置文件，一般不修改名字，后面会讲到怎么使用。
- egretProperties是我们这个项目的一些配置记录信息，一般我们不需要动它。
- index.html就是我们游戏的主界面文件。

**打开 index.html**
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200413195024.png)

将data-show-fps和data-show-log设成true，我们就可以看到游戏运行的实时帧率了。

帧率可以检测游戏运行的流畅度（性能），渲染数量越多，同时计算量越大，性能就越差，掉帧就越多。所以帧率越高，则表示性能越好，流畅度高。

另外，在底部，我们还能看到一个egret.runEgret({})的运行方法，在这里，可以修改egret的渲染模式，默认是"webgl"。

## 最常用的几个 api

### 关于文本
``` js
let label:egret.TextField = new egret.TextField(); 
label.text = "hello world!"; 
```
### 关于图片
``` js
let img:egret.Bitmap = new egret.Bitmap();
img.texture = RES.getRes("imgName");
```

### 关于形状
``` js
// 画个红色矩形框
let shp:egret.Shape = new egret.Shape();
shp.graphics.beginFill( 0xff0000, 1);
shp.graphics.drawRect( 0, 0, 100, 200 );
shp.graphics.endFill();
```

### 关于声音
``` js
// 画个红色矩形框
let sound:egret.Sound = RES.getRes("mp3Name");
sound.play();
sound.stop();
```

### 关于事件
``` js
// 触摸事件（相当于点击）
this.addEventListener(egret.TouchEvent.TOUCH_TAP, this.onTouchTap, this);
this.removeEventListener(egret.TouchEvent.TOUCH_TAP, this.onTouchTap, this);
```

### 关于计时器
``` js
// 参数为时间间隔（ms）和执行次数
let timer:egret.Timer = new egret.Timer(500, 5); 
// 边计时边触发
timer.addEventListener(egret.TimerEvent.TIMER, this.timerFunc, this);
// 计时结束触发
timer.addEventListener(egret.TimerEvent.TIMER_COMPLETE, this.timerComFunc, this);
// 开始计时
timer.start();
// 暂停计时
timer.stop();
// 重新计时
timer.reset();
```
### 关于数据存储
``` js
// 存储数据
let key:string = "score";
let value:string = "100";
egret.localStorage.setItem(key, value);
// 读取数据
let score:string = egret.localStorage.getItem(key);
// 移除数据
egret.localStorage.removeItem(key);
// 清除所有数据
egret.localStorage.clear();
```

## 小贴士
- 1、写代码需要用 ts（规范的 js） 进行开发。
- 2、egret 游戏默认是 30 帧的。
- 3、大部分 api 是以 egret 开头的，读取资源的是用 RES。
- 4、有时候你改了代码不生效或者突然报错，别慌，试试重启大法。
- 5、长度单位是像素。
- 6、每个 egret 应有且只有一个舞台（也就是 stage 对象）。舞台是 egret 显示架构中最根本的显示容器。舞台的坐标原点位于左上角。






---
title: egret白鹭入门教程-3小时开发一款小游戏
date: 2020-04-19 10:08:36
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

<!-- more -->

## 开发准备

**开发工具**

- 引擎库管理工具 EgretLauncher [下载地址](https://docs.egret.com/engine) 安装后，切换到引擎面板，选择最新的引擎版本下载

- IDE代码工具选择vscode下载安装

- 代码调试（Egret Inspector）[进行界面与代码调试]


**图片素材**
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200419101404.png)

## 开发目录
``` ts
app
├── egretProperties.json
├── favicon.ico
├── index.html 
├── libs
├── manifest.json
├── README.md
├── resource
│   ├── assets
│   └── default.res.json
├── src    //开发目录
│   ├── api //http封装与接口
│   │   ├── Api.ts
│   │   └── Http.ts
│   ├── common//公共组件与类
│   │   ├── GameEvent.ts
│   │   ├── Point.ts
│   │   └── Utils.ts
│   ├── game
│   │   ├── Example.ts
│   │   └── TimerPanel.ts
│   ├── Main.ts //主场景入口
│   ├── scene   //其它各场景
│   │   ├── BaseScene.ts
│   │   ├── EndScenes.ts
│   │   ├── LoadingScene.ts
│   │   ├── PlayScene.ts
│   │   ├── SceneController.ts
│   │   └── StartScene.ts
│   └── store
│       └── Data.ts
├── template
└── tsconfig.json
```

[仓库地址](https://github.com/RocWangPeng/egret-demo)


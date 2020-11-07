---
title: nuxt项目打包部署
date: 2020-08-03 20:53:04
tags:
categories:
---


## 首先在本地构建项目
```
npm run build
```

## 上传文件

构建成功后，会生成.nuxt的隐藏文件夹。将此文件夹以及nuxt.config.js、static和package.json一起上传到服务器

## 安装依赖
然后进入对应目录，执行
```
npm install
```

## 启动
```
nuxt start
```
这样就可以通过localhost:3000访问项目了

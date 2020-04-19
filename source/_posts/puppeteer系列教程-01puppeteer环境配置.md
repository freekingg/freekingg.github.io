---
title: puppeteer系列教程-Puppeteer 初探
date: 2020-04-03 12:44:33
tags:
- puppeteer
categories:
- [工具]
- [puppeteer]
---



## Puppeteer是什么？
Puppeteer是一个Node库，它提供了一个高级API来通过DevTools协议控制无头 Chrome或Chromium ，它也可以配置为使用完整（非无头）Chrome或Chromium。

你可以通过Puppeteer的提供的api直接控制Chrome模拟大部分用户操作来进行UI Test或者作为爬虫访问页面来收集数据。

<!-- more -->

## 为什么会产生Puppeteer呢？
很早很早之前，前端就有了对 headless 浏览器的需求，最多的应用场景有两个
- UI 自动化测试：摆脱手工浏览点击页面确认功能模式
- 爬虫：解决页面内容异步加载等问题

## Puppeteer能做什么？
你可以在浏览器中手动完成的大部分事情都可以使用Puppteer完成
比如：
- 生成页面的屏幕截图和PDF。
- 抓取SPA并生成预先呈现的内容（即“SSR”）。
- 自动表单提交，UI测试，键盘输入等。
- 创建一个最新的自动化测试环境。使用最新的的JavaScript和浏览器功能，直接在最新版本的Chrome浏览器中运行测试。
- 捕获您网站的时间线跟踪，以帮助诊断性能问题。

## 文档
- [文档](https://zhaoqize.github.io/puppeteer-api-zh_CN/#/)

## 安装

- 安装node.js  https://nodejs.org/en/
- 配置npm 源为国内淘宝源
    ``` js
    npm config set registry=http://registry.npm.taobao.org
     ```
- 设置puppeteer 中chromuim 下载地址为国内镜像
    ``` js
    npm config set puppeteer_download_host=https://npm.taobao.org/mirrors
    ```
- 安装 puppeteer
    ``` js
   npm install puppeteer
    或者
    yarn add puppeteer
    ```
- 运行
    ``` js
    <!-- app.js -->
    const puppeteer = require('puppeteer');

    puppeteer.launch({headless:false,defaultViewport:{width:1366,height:768}}).then((browser)=>{
        browser.newPage().then(page=>{
            page.goto('https://www.baidu.com')
        })
    })
    ```
    ``` shell
    node app.js
    ```

**初试 Puppeteer，截个图吧**
``` js
const puppeteer = require('puppeteer');

(async () => {
  const browser = await (puppeteer.launch({
    //设置超时时间
    timeout: 15000,
    //如果是访问https页面 此属性会忽略https错误
    ignoreHTTPSErrors: true,
    // 打开开发者工具, 当此值为true时, headless总为false
    devtools: false,
    // 关闭headless模式, 不会打开浏览器
    headless: false
  }));
  const page = await browser.newPage();
  await page.goto('https://www.jianshu.com/u/40909ea33e50');
  await page.screenshot({
    path: 'jianshu.png',
    type: 'png',
    // quality: 100, 只对jpg有效
    fullPage: true,
    // 指定区域截图，clip和fullPage两者只能设置一个
    // clip: {
    //   x: 0,
    //   y: 0,
    //   width: 1000,
    //   height: 40
    // }
  });
  browser.close();
})();
```
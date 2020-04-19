---
title: puppeteer实践-自动推荐思否的热门文章到掘金-ui自动化测试
date: 2020-04-03 17:15:50
tags:
- puppeteer
- 自动化测试
categories:
- puppeteer
---
# 爬取'思否'热门文章并转发到'掘金'

## 1、Puppeteer 简介

Puppeteer 是一个node库，他提供了一组用来操纵Chrome的API, 通俗来说就是一个 headless chrome浏览器 (当然你也可以配置成有UI的，默认是没有的)。既然是浏览器，那么我们手工可以在浏览器上做的事情 Puppeteer 都能胜任, 另外，Puppeteer 翻译成中文是”木偶”意思，所以听名字就知道，操纵起来很方便，你可以很方便的操纵她去实现：

**Puppeteer能做什么**
- 对网页进行截图保存为图片或 pdf
- 抓取单页应用(SPA)执行并渲染(解决传统 HTTP 爬虫抓取单页应用难以处理异步请求的问题)
- 做表单的自动提交、UI的自动化测试、模拟键盘输入等
- 用浏览器自带的一些调试工具和性能分析工具帮助我们分析问题
- 写爬虫做你想做的事情~
<!-- more -->

**先上动图/视频看效果**
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/sf-jj%20(1).gif)

**开始介绍，第一步，爬取 segmentfault 前30篇热门文章**
- 跳转到https://segmentfault.com/news/frontend
- 接着分析SF首页的Dom结构，爬取每篇文章的链接
- 然后取出每篇文章最重要的 href，title 等信息

具体代码如下：

``` js
 await page.goto('https://segmentfault.com/news/frontend')

    var SfFeArticleList = await page.evaluate(() => {
        var list = [...document.querySelectorAll('.news__list .news__item-title a')]
        return list.map(el => {
            return {href: el.href.trim(), title: el.innerText}
        })
    })

    await page.screenshot({path: './sf-juejin/sf.png', type: 'png'});
```
**登录掘金 (这里我事先注册了个测试账号,大家可以替换成自己的)**
- 跳转到掘金，模拟点击登录按钮
- 接着，会弹出一个的登录dialog，模拟输入用户名密码
- 模拟点击登录

代码如下：
``` js
await page.goto('https://juejin.im')

var login = await page.$('.login')
await login.click()

var loginPhoneOrEmail = await page.$('[name=loginPhoneOrEmail]')
await loginPhoneOrEmail.click()
await page.type('18516697699@163.com', {delay: 20})

var password = await page.$('[placeholder=请输入密码]')
await password.click()
await page.type('123456', {delay: 20})

var authLogin = await page.$('.panel .btn')
await authLogin.click()
```
**.推荐文章（使用第一步从SF爬取的文章信息）**

- 模拟点击推荐文章 按钮 “＋”
- 这时从SF拿到的文章信息就派上用场了，随机取出一篇: Math.floor(Math.random() * 30)
- 模拟填写推荐表单，点击发布
- 有时会提示该文章已被分享，那就换一篇吧，再执行一次。

代码如下
``` js
var seed = Math.floor(Math.random() * 30)
var theArtile = SfFeArticleList[seed]

var add = await page.$('.main-nav .ion-android-add')
await add.click()

var shareUrl = await page.$('.entry-form-input .url-input')
await shareUrl.click()
await page.type(theArtile.href, {delay: 20})

await page.press('Tab')
await page.type(theArtile.title, {delay: 20})

await page.press('Tab')
await page.type(theArtile.title, {delay: 20})

await page.evaluate(() => {
    let li = [...document.querySelectorAll('.category-list-box .category-list .item')]
    li.forEach(el => {
        if (el.innerText == '前端')
            el.click()
    })
})

var submitBtn = await page.$('.submit-btn')
await submitBtn.click()
```

## 完整代码

``` js
const puppeteer = require('puppeteer')

function timeout(delay) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            try {
                resolve(1)
            } catch (e) {
                reject(0)
            }
        }, delay)
    })
  }

var delay = 1000
// 以下拿掘金开刀,贡献私人测试账号
puppeteer.launch({headless: false}).then(async browser => {
    var page = await browser.newPage()
    page.setViewport({width: 1200, height: 600})

    /** 1. 到sf获取最新的前端文章 **/
    try {
        await page.goto('https://segmentfault.com/news/frontend')
        await timeout(delay)

        var SfFeArticleList = await page.evaluate(() => {
            var list = [...document.querySelectorAll('.news__list .news__item-title a')]

            return list.map(el => {
                return {href: el.href.trim(), title: el.innerText}
            })
        })

        console.log('SfFeArticleList:', SfFeArticleList);

        await page.screenshot({path: './data/sf-juejin/sf.png', type: 'png'});
    } catch (e) {
        console.log('sf err:', e);
    }

    /** 登录juejin **/
    try {
        await timeout(3000)
        await page.goto('https://juejin.im')
        await timeout(3000)

        var login = await page.$('.login')
        await login.click()

        var loginPhoneOrEmail = await page.$('[name=loginPhoneOrEmail]')
        console.log('loginPhoneOrEmail:', loginPhoneOrEmail);
        await loginPhoneOrEmail.click()
        await page.type('18516697699@163.com', {delay: 20})

        var password = await page.$('[placeholder=请输入密码]')
        console.log('password:', password);
        await password.click()
        await page.type('aaa123456', {delay: 20})

        var authLogin = await page.$('.panel .btn')
        console.log('authLogin:', authLogin);
        await authLogin.click()

    } catch (e) {}

    /** 随机推荐一篇从sf拿来的文章到掘金 **/
    try {
        await timeout(2500)
        var seed = Math.floor(Math.random() * 30)
        var theArtile = SfFeArticleList[seed]

        var add = await page.$('.main-nav .more')
        await add.click()

        var addLink = await page.$('.more-list .item')
        await addLink.click()

        await timeout(2500)

        var shareUrl = await page.$('.entry-form-input .url-input')
        await shareUrl.click()
        await page.type(theArtile.href, {delay: 20})

        await page.press('Tab')
        await page.type(theArtile.title, {delay: 20})

        await page.press('Tab')
        await page.type(theArtile.title, {delay: 20})

        await page.evaluate(() => {
            let li = [...document.querySelectorAll('.category-list-box .category-list .item')]
            li.forEach(el => {
                if (el.innerText == '前端')
                    el.click()
            })
        })

        var submitBtn = await page.$('.submit-btn')
        await submitBtn.click()

    } catch (e) {
        await page.screenshot({path: './data/sf-juejin/err.png', type: 'png'});
    }

    await page.screenshot({path: './data/sf-juejin/done.png', type: 'png'});
    // await page.close()
    // browser.close()
})
```
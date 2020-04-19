---
title: puppeteer实践-批量下载图灵图书
date: 2020-04-03 16:16:30
tags:
- puppeteer
- 自动化测试
categories:
- puppeteer
---
# puppeteer实践-批量下载图灵图书

## 下载图灵图书

借助无头浏览器 Puppeteer 的无头模式，实现简单的下载已购买书籍为 pdf 到本地的脚本，支持批量下载已购买的书籍。

**Puppeteer能做什么**
- 对网页进行截图保存为图片或 pdf
- 抓取单页应用(SPA)执行并渲染(解决传统 HTTP 爬虫抓取单页应用难以处理异步请求的问题)
- 做表单的自动提交、UI的自动化测试、模拟键盘输入等
- 用浏览器自带的一些调试工具和性能分析工具帮助我们分析问题
- 写爬虫做你想做的事情~

<!-- more -->

## 实现流程
- 启动浏览器
- 跳转登陆页
- 输入账号密码
- 登陆成功
- 找到已买的书籍
- 逐次点击书籍名称，进入书籍详情
- 将详情生成pdf，并保存

使用方法，传入帐号密码和保存路径，如：
``` js
node ./demo/download-ituring-books.js '用户名' '密码' './books'
```
> 注意：puppeteer 的 Page.pdf() 目前仅支持在无头模式中使用，所以要想看有头状态的抓取过程的话，执行到 Page.pdf() 这步会先报错

所以启动这个脚本时，需要保持无头模式：
``` js
const browser = await puppeteer.launch({
    // 关闭无头模式，方便我们看到这个无头浏览器执行的过程
    // 注意若调用了 Page.pdf 即保存为 pdf，则需要保持为无头模式
    // headless: false,
});
```
看下执行效果：
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200403162959.png)

下载完后是这样子：
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200403163112.png)

## 完整代码
download-ituring-books.js
``` js
/**
 * 下载图灵电子书
 */
const puppeteer = require('puppeteer');
const mkdirp = require('mkdirp');
const path = require('path');
const BASE_URL = 'http://www.ituring.com.cn';
const SHELF_URL = `${BASE_URL}/user/shelf`;
const LOGIN_URL = `http://account.ituring.com.cn/log-in?returnUrl=${encodeURIComponent(SHELF_URL)}`;

const printIturingBooks = async (userName, password, saveDir = './books/') => {
    if (!userName) {
        throw new Error('请输入用户名');
    }
    if (!password) {
        throw new Error('请输入密码');
    }
    try {
        // 设置统一的视窗大小
        const viewport = {
            width: 1376,
            height: 768,
        };

        console.log('启动浏览器');
        const browser = await puppeteer.launch({
            // 关闭无头模式，方便我们看到这个无头浏览器执行的过程
            // 注意若调用了 Page.pdf 即保存为 pdf，则需要保持为无头模式
            // headless: false,
        });

        console.log('打开新页面');
        const page = await browser.newPage();
        page.setViewport(viewport);

        console.log('输入登录地址');
        await page.goto(LOGIN_URL);

        await page.waitForSelector('#loginForm');

        console.log('输入用户名和密码');
        await page.focus('#Email');
        await page.type(userName);
        await page.focus('#Password');
        await page.type(password);
        await page.click('#loginForm  input[type="submit"]');

        await page.waitForSelector('.block-items');
        const books = await page.$eval('.block-items', element => {
            const booksHTMLCollection = element.querySelectorAll('.block-item');
            const booksElementArray = Array.prototype.slice.call(booksHTMLCollection);
            const books = booksElementArray.map(item => {
                const a = item.querySelector('.book-img a');
                return {
                    href: a.getAttribute('href'),
                    title: a.getAttribute('title'),
                };
            });
            return books;
        });
        console.log(`书架上共找到${books.length}本书`);

        for (let book of books) {
            const bookPage = await browser.newPage();
            bookPage.setViewport(viewport);
            await bookPage.goto(`${BASE_URL}/${book.href}`);
            await bookPage.waitForSelector('.bookmenu');
            const articles = await bookPage.$eval('.bookmenu table tbody', element => {
                const articlesHTMLCollection = element.querySelectorAll('tr');
                const articlesElementArray = Array.prototype.slice.call(articlesHTMLCollection);
                const articles = articlesElementArray.map(item => {
                    const a = item.querySelector('td a');
                    return {
                        href: a.getAttribute('href'),
                        title: a.innerText.trim(),
                    };
                });
                return articles;
            });
            bookPage.close();

            for (let article of articles) {
                const articlePage = await browser.newPage();
                articlePage.setViewport(viewport);
                await articlePage.goto(`${BASE_URL}/${article.href}`);
                await articlePage.waitForSelector('.article-detail');
                await articlePage.$eval('body', body => {
                    body.querySelector('.layout-head').style.display = 'none';
                    body.querySelector('.book-page .side').style.display = 'none';
                    body.querySelector('#footer').style.display = 'none';
                    body.querySelector('#toTop').style.display = 'none';
                    Promise.resolve();
                });
                const dirPath = `${saveDir}/${book.title}`;
                const fileName = `${article.title.replace(/\//g, '、')}.pdf`;
                const filePath = `${dirPath}/${fileName}`;
                mkdirp.sync(dirPath);
                await page.emulateMedia('screen');
                await articlePage.pdf({
                    path: filePath,
                    format: 'A4'
                });
                console.log(`保存成功: ${filePath}`);
                articlePage.close();
            }
        }

        browser.close();
    } catch (e) {
        console.error(e);
    }
};

const USER = process.argv[2];
const PASSWORD = process.argv[3];
const SAVE_DIR = process.argv[4];

if (!USER || !PASSWORD) {
    console.log('invalid user or password');
    process.exit();
}

printIturingBooks(USER, PASSWORD, SAVE_DIR);
```
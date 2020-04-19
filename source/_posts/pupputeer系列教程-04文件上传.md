---
title: pupputeer系列教程-04文件上传
date: 2020-04-03 12:57:59
tags:
- puppeteer
categories:
- [工具]
- [puppeteer]
---

``` js
const puppeteer = require('puppeteer')

async function upload(){
    const browser = await puppeteer.launch({headless:false});
    const page = await browser.newPage();
    await page.goto('https://www.baidu.com');
    const soutuBtn = await page.waitForSelector('span.soutu-btn');
    await soutuBtn.click();
    const uploadPic = await page.waitForSelector('input.upload-pic')
    await uploadPic.uploadFile('C:\\Users\\zyzhao\\Documents\\puppeteer_teach\\puppeteer.png')
}

upload();
```
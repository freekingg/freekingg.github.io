---
title: puppeteer系列教程-05处理多个元素
date: 2020-04-03 13:00:13
tags:
- puppeteer
categories:
- [工具]
- [puppeteer]
---

## 多个元素处理
- $$()
- $$eval()
代码片段
京东手机列表
``` js
const puppeteer = require('puppeteer')

async function jd(){
    const browser = await puppeteer.launch({headless:false});
    const page = await browser.newPage();
    await page.goto('https://www.jd.com');

    const input = await page.$('#key')
    await input.type('手机');
    await page.keyboard.press('Enter');

    await page.waitForSelector('ul.gl-warp>li');

    const list = await page.$$eval('ul.gl-warp>li',eles=>eles.map(ele=>ele.innerText))
    console.log('List ==',list);
}

jd();
```
---
title: puppeteer系列教程-10切换浏览器tab页
date: 2020-04-03 14:06:22
tags:
- puppeteer
categories:
- [工具]
- [puppeteer]
---

## 切换浏览器tab页
`browser.waitForTarget`

``` js

const puppeteer = require('puppeteer')

async function switchWindows(){
    const browser = await puppeteer.launch({headless:false});
    const page = await browser.newPage();
    await page.goto('http://news.baidu.com/',{waitUntil:'networkidle2'});

    const link = await page.waitForSelector('li.hdline0> strong')
    await link.click();

    const target= await browser.waitForTarget(target=>target.url().includes('cpc.people.com.cn'));
    const newpage = await target.page();

    await newpage.waitForSelector('div.text_con.text_con01');
    const innerText = await newpage.$eval('div.text_con.text_con01',ele=>ele.innerText);
    console.log(innerText);
}
switchWindows();
```
---
title: puppeteer系列教程-12执行JavaScript方法
date: 2020-04-03 14:07:44
tags:
- puppeteer
categories:
- [工具]
- [puppeteer]
---
## 执行JavaScript方法

``` js
const puppeteer = require('puppeteer')

async function run(){
    const browser = await puppeteer.launch({headless:false});
    const page = await browser.newPage();
    await page.goto('https://www.ctrip.com/',{waitUntil:'networkidle2'});


    await page.evaluate(()=>{
        document.querySelector('#HD_CheckIn').value = "2019-7-20"
    })

}

run();

```

---
title: puppeteer系列教程-02元素获取以及操作
date: 2020-04-03 12:50:54
tags:
- puppeteer
categories:
- [工具]
- [puppeteer]
---

## 获取元素
使用 $ 方法获取，输入元素的css selector定位元素。
``` js
// 获取页面中的id为kw的dom元素
const input_area = await page.$("#kw");
```

## 操作-输入点击
- type 输入文本
- click 元素点击
``` js
const puppeteer = require('puppeteer');

// puppeteer.launch({headless:false,defaultViewport:{width:1366,height:768}}).then((browser)=>{
//     browser.newPage().then(page=>{
//         page.goto('https://www.baidu.com')
//     })
// })

async function run (){
    const browser = await puppeteer.launch({headless:false,defaultViewport:{width:1366,height:768}});
    const page = await browser.newPage();
    await page.goto('https://www.baidu.com');
    const input_area = await page.$("#kw");
    await input_area.type("Hello Wrold");

    const search_btn = await page.$('#su');
    await search_btn.click();

}

run();
```
---
title: puppeteer系列教程-03获取文本值
date: 2020-04-03 12:55:29
tags:
- puppeteer
categories:
- [工具]
- [puppeteer]
---

## 获取文本值
$eval(selector,pagefunction)

``` js
const puppeteer = require('puppeteer');

async function run (){
    const browser = await puppeteer.launch({headless:false,defaultViewport:{width:1366,height:768}});
    const page = await browser.newPage();
    await page.goto('https://www.baidu.com');
    const input_area = await page.$("#kw");
    await input_area.type("Hello Wrold");

    const search_btn = await page.$('#su');
    await search_btn.click();

    await page.waitFor('div#content_left > div.result-op.c-container.xpath-log',{visible:true});

    let resultText = await page.$eval('div#content_left > div.result-op.c-container.xpath-log',ele=> ele.innerText)
    console.log("result Text= ",resultText);
}

run();
```


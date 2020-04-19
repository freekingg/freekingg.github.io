---
title: puppeteer系列教程-07拖拽验证码操作
date: 2020-04-03 13:03:24
tags:
- puppeteer
categories:
- [工具]
- [puppeteer]
---

## 拖拽操作

使用 api `mouse`

自动化操作阿里云注册页面的验证码部分
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200403130504.png)
``` js
const puppeteer = require('puppeteer')

async function aliyun(){
    const browser = await puppeteer.launch({headless:false,ignoreDefaultArgs:['--enable-automation']});
    const page = await browser.newPage();
    await page.goto('https://account.aliyun.com/register/register.htm',{waitUntil:"networkidle2"});

    const frame = await page.frames().find(frame=>{
        console.log(frame.url())
        return frame.url().includes('https://passport.aliyun.com/member/reg/fast/fast_reg.htm')

    })

    const span = await frame.waitForSelector('#nc_1_n1z');
    const spaninfo = await span.boundingBox();
    console.log('spaninfo',spaninfo)
    
    await page.mouse.move(spaninfo.x,spaninfo.y);
    await page.mouse.down();

    const div = await frame.waitForSelector('div#nc_1__scale_text > span.nc-lang-cnt');
    const divinfo = await div.boundingBox();

    console.log('divinfo',divinfo)
    for(var i=0;i<divinfo.width;i++){
        await page.mouse.move(spaninfo.x+i,spaninfo.y);
    }
    await page.mouse.up();
}

aliyun();
```
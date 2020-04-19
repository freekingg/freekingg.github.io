---
title: puppeteer系列教程-09模拟快捷键操作
date: 2020-04-03 14:05:41
tags:
- puppeteer
categories:
- [工具]
- [puppeteer]
---
## 模拟快捷键

使用api `page.keyborder`

``` js
const puppeteer = require('puppeteer');


async function run(){
    const browser = await puppeteer.launch({
        headless:false,
        defaultViewport:{width:1200,height:700}, 
        ignoreDefaultArgs:['--enable-automation'],
        slowMo:200,
        args:['--window-size=1200,700']})
    const page = await browser.newPage();
    
    await page.goto('http://39.107.96.138:3000/signin',{waitUntil:'networkidle2'});

    const username = await page.waitForSelector('#name');
    await username.click();
    await username.type('user1');

    const passwd = await page.waitForSelector('#pass');
    await passwd.click();
    await passwd.type('123456');

    const loginBtn = await page.waitForSelector('.span-primary');
    await loginBtn.click();

    const createTopicBtn = await page.waitForSelector('#create_topic_btn');
    await createTopicBtn.click();

    const inputTopic = await page.waitForSelector('.CodeMirror-code');
    await inputTopic.click();
    await page.keyboard.type('helloworld');

    // ctrl + a
    await page.keyboard.down('Control');
    await page.keyboard.down('a');
    await page.keyboard.up('a');
    await page.keyboard.down('Control');


    await page.keyboard.down('Control');
    await page.keyboard.down('b');
    await page.keyboard.up('b');
    await page.keyboard.down('Control');
    
    
}
run();
```
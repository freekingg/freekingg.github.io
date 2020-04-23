---
title: puppeteer实践-qq音乐自动购买的ui自动化测试
date: 2020-04-03 16:51:12
tags:
- puppeteer
- 自动化测试
- 案例
categories:
- [puppeteer]
- [案例]
---

## 1、Puppeteer 简介

Puppeteer 是一个node库，他提供了一组用来操纵Chrome的API, 通俗来说就是一个 headless chrome浏览器 (当然你也可以配置成有UI的，默认是没有的)。既然是浏览器，那么我们手工可以在浏览器上做的事情 Puppeteer 都能胜任, 另外，Puppeteer 翻译成中文是”木偶”意思，所以听名字就知道，操纵起来很方便，你可以很方便的操纵她去实现：

**Puppeteer能做什么**
- 对网页进行截图保存为图片或 pdf
- 抓取单页应用(SPA)执行并渲染(解决传统 HTTP 爬虫抓取单页应用难以处理异步请求的问题)
- 做表单的自动提交、UI的自动化测试、模拟键盘输入等
- 用浏览器自带的一些调试工具和性能分析工具帮助我们分析问题
- 写爬虫做你想做的事情~
<!-- more -->
## 安装
``` js
yarn add puppeteer
# or "npm i puppeteer"
```

## 实践
### 初始化
建立 index.js
``` js
const puppeteer = require('puppeteer');

(async () => {
      const browser = await puppeteer.launch();
    const page = await browser.newPage();
    await page.goto('https://y.qq.com');
    await page.screenshot({path: 'yqq.png'});
    browser.close();
})();
```

执行
``` js
node  index.js
```
这段代码会打开 https://y.qq.com 并截图，我们运行

如果看到目录下有生成图片y.qq.png的话，恭喜你，我们可以开始继续往下


**这里测试的功能是自动拉登录购买一张数字专辑，并在购买成功后跳转到铭牌页，先看下整个流程吧。**

首先我们先创建一个设备，文档中(https://github.com/GoogleChrome/puppeteer/blob/master/DeviceDescriptors.js)

我们能看到，默认支持的设备数量还是很多的，除了这些默认的设备之外，我们还可以自定义自己的设备，后面在调用emulate方法时会提到：

我们这里暂时先创建系统提供的iphone6设备，完了我们定义一个延时的timeout函数

``` js
const puppeteer = require('puppeteer');
const devices = require('puppeteer/DeviceDescriptors');
const iPhone = devices['iPhone 6'];
let timeout = function (delay) {
     return new Promise((resolve, reject) => {   
           setTimeout(() => {   
                  try {
                      resolve(1)
                  } catch (e) {
                      reject(0)
                   }
           }, delay);
     })
 }
 ```

 接下来我们创建一个浏览器实例，并打开一个页面

 ``` js
 const browser = await puppeteer.launch({
      headless:false //设置为true就不会打开浏览器
 });

 const page = await browser.newPage();
 ```

 ## 测试步骤
 1、**第一步:我们打开页面，考虑到有数据需要异步加载，我们在延迟1000ms后调用screenshot方法截图留作日志。**
 ``` js
 console.log("进入页面");
await page.goto('https://y.qq.com/m/digitalbum/gold/index.html?_video=true&id=2210323&g_f=tuijiannewupload#index/fans');
await timeout(1000);
await page.screenshot({
     path: '1.png'
 });
 ```

 2、**第二步: 模拟触发点击 立即购买按钮，这时候会因为没有登录态而打开QQ登录。**
 ``` js
 console.log("点击立即购买按钮");
await page.tap('.js_sale_buyalbum');
await page.screenshot({
    path: '2.png'
 });
```
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200403170216.png)

3、**在输入框中输入帐号密码**
模拟输入需要我们先调用tap方法模拟点击输入框，tap参数就是元素selector,再用type方法进行输入,输入完了之后在模拟点击登录按钮，登录完了之后我们延迟一段时间截图，顺利的话我们就能重新回到之前的售卖页首页，而底下usrbar因为有了登录态也展示了出来。
``` js
console.log("登录");
await page.tap("#u"); //直接操作dom选择器，是不是很方便
await page.type("521017853");

await page.tap("#p");
await page.type("*********");//这里密码就不展示了哈

await page.tap("#go");

await timeout(3000);

await page.screenshot({
     path: '3.png'
});

console.log("登录成功");
```

![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200403170315.png)

4、**点击立即购买按钮**
这里会出现一个购买选择浮层,然后我们点击立即支付之后需要加载米大师，故这里我们延迟5000ms。
```js
//点击购买
console.log("点击立即购买按钮");
await page.tap('.js_sale_buyalbum');

await page.screenshot({
    path: '4.png'
});

console.log("点击支付浮层上的立即支付");
await page.tap(".js_buyalbum_pay");

await timeout(5000);
```
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200403170416.png)

5、**点击提示中的确定按钮**
由于米大师是在iframe中打开的，所以我们需要先获取到我们当前页frame,这个可以调用刚创建的页面实例page的mainFrame()方法即可获得,如果我们需要获取子frame的话也只需要调用childFrames来进行获取。在获取到米大师对应的frame之后就可以调用midas_frame.$(selector)类jquery的方法进行元素的获取，之后再模拟点击。
``` js
console.log("进入 米大师支付浮层")
await page.screenshot({
     path: '5.png'
});

let $frame = page.mainFrame();
let midas_frame = $frame.childFrames()[0];//获取到midas对应的frame

console.log("点击确定 米大师支付浮层测试环境提示 的确认按钮");
let $dom = await midas_frame.$(".fusion-pm-fl-wrapper .fpm-default");
await $dom.tap();

await page.screenshot({
    path: '6.png'
});
```
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200403170600.png)

6、**点击Q币支付**
``` js
console.log("点击 米大师支付浮层 确认支付按钮");
$dom = await midas_frame.$("#wrap .fpm-default");
await $dom.tap();

await timeout(5000);

await page.screenshot({
    path: '7.png'
});
```
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200403170733.png)

7、**测试完毕，关闭浏览器实例**
``` js
console.log("点击 米大师支付浮层 支付完成");
$dom = await midas_frame.$("#wrap .btn-primary");
await $dom.tap();

await timeout(2000);

console.log("已购铭牌页");
await page.screenshot({
     path: '8.png'
});
browser.close();
```

**最后在项目目录中，我们看到，各个步骤的截图都已生成。**
![](https://raw.githubusercontent.com/RocWangPeng/king-static/master/20200403170840.png)


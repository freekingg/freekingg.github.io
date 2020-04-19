---
title: puppeteer系列教程-11处理alert窗口
date: 2020-04-03 14:07:03
tags:
- puppeteer
categories:
- [工具]
- [puppeteer]
---

## 处理alert窗口
dialog api

``` js
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>

<body>

    <p>点击显示一个确认对话框.</p>

    <button id='btn1' onclick="myFunction1()">点击</button>



    <p>点击显示一个输入文本的对话框.</p>

    <button id="btn2" onclick="myFunction2()">Try it</button>

    <p id="demo"></p>

    <script>
        function myFunction1() {
            confirm("Press a button!");
        }
        function myFunction2() {
            var person = prompt("Please enter your name", "Harry Potter");
            if (person != null) {
                document.getElementById("demo").innerHTML =
                    "Hello " + person + "! How are you today?";
            }
        }
    </script>

</body>

</html>
```
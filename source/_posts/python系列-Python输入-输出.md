---
title: python系列-Python输入-输出
date: 2020-03-31 16:08:02
tags:
- python
categories:
- python
---

## 输入
### print 函数
语法
``` python
print(values, sep, end, file, flush)
```
**values**
需要输出的值
多个值, 使用 "," 进行分割

**sep**
分割符
多个值, 被输出出来之后, 值与值之间, 会添加指定的分隔符

**end**
输出完毕之后, 以指定的字符, 结束
默认是换行 '\n'

**file**
表示输出的目标
默认是标准的输出(控制台)`file=sys.stdout`
还可以是一个可写入的文件句柄
``` python
f = open("xxx", "w")
file=f
```

**flush**
表示立即输出的意思
值为Bool类型,默认False


## 使用场景
### 输出一个值
``` python
print(123)
```

### 输出一个变量
``` python
num = 55
print(num)
```

### 输出多个变量
``` python
num = 55
num2 = 155
print(num,num2)
```

### 格式化输出
``` python
name = 'sz'
age = 18
我的名字是xxx, 年龄是xxx
print("我的名字是%s, 年龄是%d"%(name, age))
print("我的名字是{0}, 年龄是{1}".format(name, age))
```

### 输出到文件中
``` python
print >>open("test.txt", "w"), "12345"
f = open("test.txt", "w")
print("xxxxxxxx", file=sys.stdout)
```

### 输出不自动换行
``` python
print("abc", end="")
```

### 输出,使用分隔符分割
``` python
print("1", "2", "3", sep="&&&&&")
```
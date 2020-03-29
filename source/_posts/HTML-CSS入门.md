---
title: HTML&CSS入门
date: 2016-03-29 14:42:59
tags:
- javascript
- css
categories:
- 前端
---

HTML&CSS基础入门总结


## 一、使用 css 控制页面样式的方式
``` javascript
//行内样式
<div style="属性1:值1;属性2:值2;"></div>
//嵌入样式
<style type="text/css">
选择器{属性1:值1;属性2:值2;}
</style>
//外部样式
<link rel="stylesheet" type="text/css" href="url">
//导入样式
import url(外部样式表位置)；

```
<!--more-->

## 二、css 选择器
>当我们定义一条样式规则时候，这条样式规则会作用于网页当中
的某些元素，而我们的规定的这些元素的规则就叫做选择器

•  **id选择器**
•  **类选择器**
•  **标签选择器**
• 交叉选择器
• 群组选择器
• 后代选择器 (包含选择器 )
• 通用选择器

## 三、css 的继承性和叠加性

>**继承性**
后代元素会继承前辈元素的一些文字属性和样式

>**叠加性**
同一个元素，被多个样式规则指定。
因为css的继承性和叠加性，就有了css优先级的概念

## 四、选择器的优先级
>所谓的优先级，指的就是哪条样式规则会最终作用于指定的元素

``` css
- 在属性后面使用 !important 会覆盖页面内任何位置定义的元素样式。
- 作为style属性写在元素内的样式
- id选择器
- 类选择器
- 标签选择器
- 通配符选择器
- 浏览器自定义或继承
```

## 五、元素类型
``` css  
>  块元素
>		div,h1-h6,p,ul,li,ol,dl,dt,dd,header,nav,footer,section,article,aside
>		特征：
>		1、默认独占一行
>		2、没有给宽度的时候，宽度是auto，撑满一行(宽度就是父级的宽度)
>		3、支持所有的css命令
>		
>	行内元素
>		a,span,strong,em,mark,img,time
>		特征：
>		1、内容撑开宽高
>			宽高的值都是auto，只不过显出来的宽高是由内容撑开的
>		2、不支持设置宽高
>		3、不支持上下的margin与上下padding(左右支持)
>			上下的padding是有问题，虽然把背影撑出来了，这个只是表面现象，它不会对其它的元素有影响
>		4、所有的行内元素都会在一行显示
>		5、代码换行会被解析成一个空格
>		
>	行内块元素
>		特征：
>			1、行内元素支持宽高
>			2、块元素可以在一行中显示
>			3、不给宽高的话，宽度会由内容撑开
>			4、代码换行会被解析成一个空格
>			5、IE6、7不支持块元素的inline-block
```
### 块元素和行元素的相互转换
``` 
块元素转为行元素
display:inline

行元素转为块元素
display:block

行元素转为行内块元素
display:inline-block

为元素设置display: none; 可以让元素隐藏起来并且不
占用页面空间,浏览器会完全忽略掉这个元素，该元素将
不会被显示，也不会占据文档中的位置
```
## 六、css 文字属性
``` css

font			文字
font-weight		文字着重
font-style		文字倾斜
font-size		文字大小（一般都为偶数）
line-height		文字行高		
font-family		字体（中文默认是宋体）

``` 
  
### 文字样式 font
``` css

font-weight			文字加粗
	weight			加粗
	normal			正常
	
font-style			文字倾斜
	italic			倾斜
	normal			正常
	
font-size			文字大小
	50px			数字加单位
	
font-family			文字字体
	"主要字体","备选字体1","备选字体2"		表示如果用户电脑上有 “主要字体”，就显示"主要字体",如果用户电脑上没有主要字体",那就显示"备选字体1",如果用户电脑没有"备选字体1",就显示"备选字体2";
	英文字体,"中文字体"		英文字符 使用英文字体，中文字符 使用 中文字体;

举例:
div{
	font-weight: bold;
	font-style: normal;
	font-size: 50px;
	font-family:arial,"华文行楷","宋体",'楷体';
}

```

### 	 行高 line-height

```  css
行高代表了一行文字在容器中所占的高度
line-height		行高	
	'16px'		值是数字加单位
	
如果行高的值与容器的高度相等，那文字就会垂直居中

测量多行文字行高的方法
	1、首先要知道文字的大小
	2、量下两行文字之间的距离
	3、拿上面量出来的距离除上2
	
	如果行高为奇数的话，文字的上边距会小一像素，下边距会多一像素
	
	12	13/2=7.5		6	7
举例:
div{
	border: 1px solid red;
	height: 30px;
	line-height: 30px;
}

```

### 	 文字复合样式

```  css

font:font-weight font-style font-size(必需)/line-height font-family(必需);

举例:
div{
	font: bold italic 26px/50px "微软雅黑";
}

```

#### 	 文本样式

```  css

文本
			
color				颜色
text-algin			文本的对齐方式
text-indent			首行缩进（em缩进字符）
text-decoration		文本修饰
letter-spacing		字母的间距
word-spacing		单词的间距（以空格来解析）
white-space			强制不换行

详细:

color				颜色
text-algin			文本的对齐方式
	left			左对齐（默认）
	center			居中对齐
	right			右对齐

text-indent			首行缩进
	32px			值是数字加单位
	
text-decoration		文本修饰
	underline		下划线
	overline		上划线
	line-through	中划线
	none

letter-spacing		字母间距
	10px			值为数字加单位
	
word-sapcing		单词间距
	10px			值为数字加单位

```
### 七、盒模型
>盒子模型是CSS的基石之一，布局最重要的概念，它
指定元素如何呈现在页面当中。网页就是由许多个盒
子通过不同的排列方式（纵向排列，横向排列，嵌套
排列）堆积而成。

>页面上的每个元素都被浏览器看成是一个矩形盒子模型是由content（内容）、padding（内边距）、margin（外边距）和border（边框）组成。

>默认情况下盒子的边框是无，背景色是透明，所以我们在默认情况下看不到盒子

![enter image description here](http://img.blog.csdn.net/20160823131751824?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


``` css
内容
盒子里面所包含的元素和内容

填充(内边距) (padding)
盒子里面的内容到盒子的边框之间的距离.
padding-left、 padding-right、 padding-top、padding-bottom

边框(border)
盒子本身没有边框(border)
border-left、 border-right、 border-top、 border-bottom

外边距(margin)
• 边框外和其它盒子之间
margin-left、 margin-right、 margin-top、
margin-bottom
```

* 边框（border）的组成：

``` css
 边框（border）的组成
	1、边框的粗细
		1px
	2、边框的样子
		solid		实线
		dashed		虚线
		dotted		点划线(不同的浏览器显示的是不一样的)
	3、边框的颜色
		red					颜色的英文单词
		#f00				颜色的16进制表示法
		rgba(255,0,0,0.5)	颜色的rgb的表示方法
		
	举例 :
	<style>
		div{
			width: 300px;
			height: 300px;
			border: 10px solid rgba(255,0,0,0.5);
		}
	</style>
```


## 八、overflow 属性
>有时候，子元素的宽高会超出父元素的尺寸，我们需要对超出
的内容做一些设置。

```
overflow			针对超出父级的内容如何显示
	visible			默认值，超出的内容会显示出来
	auto			如果内容超出了父级，那就出现滚动条。如果内容没有超出，就没有滚动条
	hidden			超出的内容隐藏掉
	scroll			不管内容有没有超出，都会出现滚动条

```

## 九、浮动 float
 
``` css

	定义			使元素脱离文档流，按照指定的方向发生移动，遇到父级的边界或者相邻的浮动元素就会停下来
	值			left、right、none
	特征
				1、块元素可以在一行显示
				2、按照一个指定的方向移动，遇到父级的边界或者相邻的浮动元素就会停下来
				3、行内元素支持宽高
				4、脱离文档流
```
###	 文档流

> 文档流
				文档中可显示的元素在排列时候的开始位置以及他们所占的区域
				因为页面中的元素分为不同的种类，所以他们会按各自的特点去显示，在页面中所占的区域不是一样的。会按从上到下，从左到右的顺序输出内容

#### 	 清除浮动

``` css

清除浮动的方法
	1、clear
	2、给父级添加高度
		有的时候是不能给父级添加高度的，所以这个方法就用不了（父级没有高度的情况下）
	3、inline-block
		具胡与上一个的清除浮动一样的总是，同时加了以后这个元素就没有办法居中了
	4、overflow:hidden;
		如果子级有定位的话，并且这个定位超出了父级的范围，那样的话就看不到了，所以不能加这个命令
	5、空标签
		空标签是没有内容，但是它的作用是用来清除浮动的，所以不符合行为、样式、结构相分离的标准
		ie6下标签是有一个最小高度19px，解决后也会有2像素的偏差
	6、br清除浮动
		与上面的问题是一样
	7、after伪类清除浮动（现在最主流的方法）

	示例:
	.clearfix{
		*zoom:1;
	}
	.clearfix:after{
		content: '';
		display: block;
		clear: both;
		height: 0;
		overflow: hidden;
		visibility: hidden;
	}
```

<br/>

## 十、定位 position

### 相对定位
``` css
position				定位
	relative			相对定位
		移动的方向
			top、right、bottom、left
		特点
			1、只加相对定位，不设置元素移动的位置，元素和之前是没有变化
			2、根据自己原来的位置计算移动的位置
			3、不脱离文档流，元素移走以后，原来的位置还会被保留
			4、加上相对定位后对原来的元素本身的特征没有影响
			5、提升层级
			
	示例:
	.div2{
		background: green;
		position: relative;
		left: 200px;
		top: 200px;
	}
```


### 绝对定位
``` css
absolute			绝对定位
	移动的方向
		top、right、bottom、left
	特点
		1、完全脱离文档流
		2、行内元素支持所有样式（与加上浮动或者inline-block以后的效果是一样的）
		3、如果父级有定位，那位置会根据父级移动。如果父级没有定位，那位置根据可视区移动
			(一般情况下，要用到绝对定位的时候，都会给父级来一个相对定位)
		4、提升层级
			5、提升层级
			
	示例:
	.div2{
		background: green;
		position: absolute;
		left: 200px;
		top: 200px;
	}
```
##  十一、背景（background）
### 背景（background）的组成：

``` css

	background				背景
	background-color		背景色
	backgounnd-image		背景图片
	background-repeat		背景图片是否重复
	background-position		背景图片的位置
	background-attachment	背景图片是否滚动
	
```
### 背景色 background-color

``` css
background-color		背景色

内容可以把容器的宽高撑开
背景不会占用容器的宽高

举例:
div{
	width: 300px;
	height: 300px;
	border: 10px solid red;
	background-color:green;
	background-color:rgba(255,107,35,1);
	background-color:#008800;		/*#080*/
}
```
### 背景图片 background-image

``` css

background-image		背景图片
		url(图片的地址)
		none			没有背景图（默认）
		
背景图默认会铺满整个容器

举例:
div{
	width: 300px;
	height: 300px;
	border: 10px solid red;
	background-image:url(images/img.jpg);
}
```
### 背景图重复 background-repeat

``` css

background-repeat			背景图是否重复平铺
		no-repeat			不重复平铺
		repeat-x			横向平铺
		repeat-y			纵向平铺
		repeat				全部平铺（默认值）
		
背景图默认会铺满整个容器

举例:
div{
	width: 300px;
	height: 300px;
	border: 10px solid red;
	background-image:url(images/img.jpg);
	background-repeat: no-repeat;
	background-repeat: repeat-x;
	background-repeat: repeat-y;
}
```

### 背景图片位置 background-position

``` css

background-position:x y;			背景图片的位置
	传数值：（背景图片离左上角的距离）
			x:
				正值		从容器的左边往右边走的距离
				负值		从容器的左边往左边走的距离
			y:
				正值		从容器的上边往下边走的距离
				负值		从容器的上边往上边走的距离
	传关键字
			x:
				left	图片在容器的左边
				center	图片在容器X轴的中心
				right	图片在容器的右边
			y:
				top		图片在容器的上边
				center	图片在容器Y轴的中心
				bottom	图片在容器的下边
				
	
	如果只传一个值的话，那另一个值默认为center
	如果两个值都不写的话，那默认为0，0点，左上角

举例:
div{
	width: 300px;
	height: 300px;
	border: 10px solid red;
	background-image:url(images/img.jpg);
	background-repeat: no-repeat;
	background-repeat: repeat-x;
	background-repeat: repeat-y;
}

```

### 背景图复合样式 background ( 推荐 )

``` css

举例:
div{
	width: 300px;
	height: 300px;
	border: 10px solid red;
	/*background-image: url(images/img.jpg);
	background-position: left top;
	background-repeat: no-repeat;
	background-attachment: scroll;*/
	background: green url(images/img.jpg) no-repeat center top scroll;
}

```

##  十二、html&html5标签
```
<h1>...</h1> 标题字(最大)
<h6>...</h6> 标题字(最小)
<ul> <li></li> </ul> 无序列表
<ol> <li></li> </ol> 有序列表
<strong>...</strong> 粗体字
<em>...</em> 		 斜体字
<span>…</span>       无意义的文字标签
<a>…</a> 			 链接标签
<img>				 图片标签

html5
新的特殊内容元素 article、footer、header、nav、section
新的表单控件，比如 calendar、date、time、email、url、search



```
##  十三、transition过渡效果


>过渡效果：
• 过渡效果允许css的属性值在一定的时间区间内平滑地过渡。这种效果可以在鼠标单击、
获得焦点或对元素任何改变中触发，并圆滑地以动画效果改变CSS的属性值
transition简写形式:
• transition: transition-property transition-duration transition-timing-function
transition-delay
transition-property:
• 指定应用过渡的属性，值为：none(没有属性改变)；all（所有属性改变）这
个也是其默认值；indent（元素属性名）。
transition-duration:
• 指定元素 转换过程的持续时间，单位为s（秒）或者ms(毫秒)
transition-delay：
• 指定动画开始执行前的延迟时间，单位为s（秒）或者ms(毫秒)

##  十四、大波练习...


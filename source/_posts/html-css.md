---
title: 页面制作笔记-CSS篇
date: 2016-01-31
tags: 
 - css
 - html
---

设置页面整体样式:

```
body{margin:0;padding:0;font:12px Microsoft YaHei}
```

背景:

```
background-color: #FFCC66;
background-image: url("butterfly.gif");
background-repeat: no-repeat;平铺背景图像 repeat-x(图像横向平铺) repeat-y(图像纵向平铺) repeat(图像横向和纵向都平铺)
background-attachment: fixed;固定背景图像 scroll（非固定的）fixed（图像是固定在屏幕上的）
background-position: right bottom;放置背景图像 x y
缩写顺序值:[background-color] | [background-image] | [background-repeat] | [background-attachment] | [background-position]
缩写:background: #FFCC66 url("butterfly.gif") no-repeat fixed right bottom;
```

字体:

```
font-style: italic;  字体样式 normal（正常）、italic（斜体）或oblique（倾斜）
font-variant: normal;字体变化 normal（正常）或small-caps（小体大写字母）
font-weight: bold;   字体浓淡 normal（正常）或bold（加粗）
font-size: 30px;字体大小
font-family: Microsoft YaHei, arial, sans-serif; 字体族
font属性的值应按以下次序书写：
缩写顺序值:font-style | font-variant | font-weight | font-size | font-family
缩写:font: italic bold 30px arial, sans-serif;
```

文本:

```
text-indent: 20px;文本缩进
text-align: center;文本对齐 left center right
text-decoration:none;文本装饰 underline(下划线) line-through(删除线) overline(上划线)
letter-spacing: 6px;字符间距
text-transform:none;文本转换 capitalize(首字母大写) uppercase(全大写) lowercase(全小写)
```

链接伪类:

```
a:link 从未访问过的链接
a:visited 已访问过的链接
a:active 激活的链接
a:hover  鼠标悬停的链接
```

盒状:

```
margin:1px 2px 3px 4px; 外边距
padding:1px 2px 3px 4px;内边距
width:1px;宽度
height:2px;高度
float:left; 浮动 属性值可以是left、right或者none
clear:both; 控制浮动元素的后继元素的行为,属性的值可以是left、right、both或none
position:absolute;top: 50px;left: 50px;  元素定位 absolute(绝对定位),relative(相对定位)  可以通过属性left、right、top和bottom来设定
z-index: 1;层次堆叠
```

边框:

```
border-width: 1px;边框宽度
border-style: solid;边框样式
border-color: blue;边框颜色
缩写:border: 1px solid blue;
```

注(CSS3中常用属性):

```
border-radius:1px 2px 3px 4px; 设置圆角
box-shadow:0 1px 0 #BABABA;  设置阴影
```


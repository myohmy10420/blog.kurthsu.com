---
title: '[CSS] div 垂直水平置中簡單的兩個方法'
date: 2017-09-04 14:24:00
categories:
 - [CSS]
tags:
 - CSS
 - 垂直水平置中
---
繼上次的{% post_link '[semantic-ui]自製semantic-ui有dimmer效果的modal' '自製Dimmer' %}突然發現一個問題，如果我的modal要垂直和水平都置中的話怎麼辦呢？

網路上一查就有一堆文章，我喜歡越簡單的越好，就介紹下面兩個：

## 子元素加上 margin: auto;
直接看範例吧～
``` html
<div class="father">
  <div class="son">Hello world!</div>
</div>
```
``` css
.father {
	position: relative;
	background-color: rgba(0, 0, 0, 0.53);
	width: 100%;
	height: 100%;
	position: fixed;
	z-index: 2;
}
.son {
	position: absolute;
	width: 100px;
	height: 50px;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	margin: auto;
	background: #fff;
	z-index: 3;
}
```

## 父元素使用 display: flex;
直接看範例吧～
``` html
<div class="father">
  <div class="son">Hello world!</div>
</div>
```
``` css
.father {
	display:flex;
	align-items:center;
	justify-content:center;
	background-color: rgba(0, 0, 0, 0.53);
	width: 100%;
	height: 100%;
	position: fixed;
	z-index: 2;
}
.son {
	position: relative;
	width: 100px;
	height: 50px;
	background: #fff;
	z-index: 3;
}
```

##參考文章
[CSS 垂直置中的七個方法](http://www.oxxostudio.tw/articles/201502/css-vertical-align-7methods.html)

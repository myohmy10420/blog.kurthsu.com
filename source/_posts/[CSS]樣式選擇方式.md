---
title: '[CSS]樣式選擇方式'
date: 2016-12-04 09:06:00
categories:
 - [CSS]
tags:
 - CSS
---
每當CSS樣式越寫越多，越寫越複雜，我們需要有清楚的腦袋知道CSS選擇的是哪些tag，這裡可以參考小資訊。

## 你可以把CSS看作選擇器
我們跟CSS溝通要選擇`P`這個原生tag
``` css style.css
p {
	color: green;
}
代表我選擇P這個Tag讓文字變成綠色的
```
### 自創id和class
到後面CSS樣式很多，同一個tag不可能都只用一樣的樣式，這時候id和class自創屬性給他們就相當的實用，而且大多數的時候建議不要擅自改變tag的原生屬性以免將來很難維護。

在html檔案裡面繕寫
``` html index.html
<div id="green">Yahoo!</div>
<p class="blue">Hello!</p>
```
在CSS裡像以下為他們冠上屬性
``` css style.css
#green {
	color: green;
}
.blue {
	color: blue;
}
```
`注意！一個id在body裡面只能使用一次！`
### 逗號(,)為一次選擇多個

``` css style.css
h1 {
	color: blue;
}

h2 {
	color: blue;
}
```

可以簡寫為
``` css style.css
h1, h2 {
	color: blue;
}
```
### 選擇一個tag裡的另一個tag
我們可以選擇一個tag裡的幾個tag改變他們的樣式
``` html index.html
<div class="content">Hello, my name is <strong class="name">Kurt</strong>, how are you?</div>
```
此時我只想改變這個content裡的name
``` css style.css
.content .name {
	color: blue;
}
```
### 選擇擁有該屬性的tag
很多時候tag會加入一些其他的元素，我們也可以利用這個元素來讓CSS選擇到此tag
``` html index.html
<h2 title="index">Hello world</h2>

<h2>Hello world</h2>
```
我們用利用給他的元素`title`讓CSS做選擇
``` css style.css
[title] {
	color: red;
}
```

當然也可以指定明確
``` css style.css
[title=index] {
	color: red;
}
```

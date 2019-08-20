---
title: '[JavaScript]fadeIn & fadeOut with inline-block'
date: 2017-08-17 02:34:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - fadeIn
 - fadeOut
 - inline-block
---
現在jQuery幾乎已經成基本配備，簡單的網頁互動已經可以很輕鬆簡單的做一個基本的版本，這樣使用者在使用網頁的時候比較不會有頁面突然被刷新而不知所以然的疑慮，簡單的介紹fadeIn和fadeOut吧！

## 直接實作吧！
先做個最簡單的東西在html~
``` html
<div class="block"></div>
<div class="block2"></div>
```
再做一個簡單的css，注意我先把block2藏起來摟(display: none)！
``` css
body {
  text-align: center;
}
.block ,.block2{
  display: inline-block;
  margin-right: 30px;
  width: 100px;
  height: 100px;
  outline: 1px solid;
}
.block {
  background-color: blue;
}
.block2 {
  background-color: red;
  display: none;
}
```
接下來js實作fadeIn和fadeOut吧！
``` javascript
function block () {
  $('.block').fadeOut(function () {
    $('.block2').fadeIn();
  });
}
function blockTwo () {
  $('.block2').fadeOut(function () {
    $('.block').fadeIn();
  });
}
```
實作出來會發現`block2並沒有置中，這是因為她的display被宣染成block而不是inline-clock了`！！
這時候只要加一段jQuery的小東西`.css()`即可，所以我們修改上面的JavaScript如下：
``` javascript
function block () {
  $('.block').fadeOut(function () {
    $('.block2').fadeIn().css("display","inline-block");
  });
}
function blockTwo () {
  $('.block2').fadeOut(function () {
    $('.block').fadeIn();
  });
}
```
可以參考[我的codepen](https://codepen.io/KurtHsu/pen/bwdZkX?editors=1111)。

## 簡單認識fadeIn和fadeOut
在文件上他有兩個參數，一個是控制動畫的時間長短(單位是ms，預設400)，另一個是執行完畢後要做的事情(function)，兩個都是一樣的做法。

所以照著剛剛上面的範例條一個出來解釋的話：
``` javascript
$('.block2').fadeOut(3000, function () {
    $('.block').fadeIn(2000);
});
```
這樣就代表block2的fadeOut算整整三千毫秒執行完後才會執行後面的function，block的fadeIn會在兩千毫秒執行完畢。

#### 參考網站
[jQuery文件](http://api.jquery.com/fadein/)
[stack overflow](https://stackoverflow.com/questions/1091322/how-to-fade-to-display-inline-block)

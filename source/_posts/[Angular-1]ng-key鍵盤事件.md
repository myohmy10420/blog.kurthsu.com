---
title: '[Angular-1]ng-key鍵盤事件'
date: 2016-12-19 15:38:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
 - Angular1
---
今天想要做一個按下Enter就可以執行Submit的事件，最常見到的地方就是登入的時候，我們帥氣快速的打完帳號密碼一定會Combo接Enter鍵登入吧！其實這是JS的事件唷！

沒想到這麼簡單的東西我會卡了四個小時，滿滿的大心得記錄一下吧XD。

## 原生Javascript的onkey事件
JS的原生事件有三種：
`onkeyup`：釋放鍵盤觸發
`onkeydown`：按下鍵盤觸發
`onkeypress`：按下鍵盤後釋放觸發

我並沒有很深入研究使用情況，我在想onkeydown和onkeypress使用場合在一次要按多顆鍵(onkeydown)或者是單顆鍵(onkeypress)，今天主要用登入表單的例子示範最基本的實作就好。

``` html index.html
<form action="#" method="post">
	請輸入帳號<input type="text" name="username" onkeypress="goLogin(evt);">
	請輸入密碼<input type="text" name="password" onkeypress="goLogin(evt);">
	<input type="submit" value="登入">
</form>
```
以上就是最簡單的登入表格囉，並且我直接在input裡面加入inline function，也就是說當使用者游標正在那兩個input裡面的時候只要按下鍵盤再放開會一直不斷執行goLogin()這個function，然後千萬不要忘記要給他一個變數讓等等的JS去判斷我們按下什麼鍵。
``` javascript app.js
function (evt) {
	if (evt.keyCode == 13) {
		dosomething...
	}
};
```
JS的function裡面要給他一個變數讓外部的直帶進去，而keyCode則是用onkeypress產生出來的東西的原生屬性，Enter鍵的這個值會是13，鍵盤對應表在網路上有很多資源，[例如這篇](https://www.cambiaresearch.com/articles/15/javascript-char-codes-key-codes)。

onkeypress當然也可以從外部呼叫，例如我在JS做一個事件：
``` javascript app.js
var userinput = document.GetElementById("username");
userinput.onkeypress = function (evt) {
	dosomething...
};
```

## AngularJs的onkey事件
AngularJs的方法其實大同小異，只是官網寫的三種教學竟然不一致讓我少看最重要的那一篇，他把我最想知道的用法寫在[ng-keyup](https://docs.angularjs.org/api/ng/directive/ngKeyup)而已太扯了。

總之剛剛上面原生講的三種事件換寫為下面三種：
`onkeyup`＝`ng-keyup`
`onkeydown`＝`ng-keydown`
`onkeypress`＝`ng-keypress`

再來直接用剛剛基本的登入頁面做示範：

``` html index.html
<form>
	請輸入帳號<input type="text" ng-model="count.username" onkeypress="goLogin($event);">
	請輸入密碼<input type="text" ng-model="count.password" onkeypress="goLogin($event);">
	<input type="submit" value="登入">
</form>
```
而JS裡面則差不多一樣
``` javascript app.js
$scope.goLogin = function (keyValue) {
	if (keyValue.keyCode == 13) {
		dosomething...
	}
};
```

Angular的ng-model方便之處在這邊就不多談，最重要要注意的是goLogin(\$event)我裡面待的變數是Angular原生的`$event`唷！！！！！！！！！

總之他們兩個一定要搭配就是了，而鍵盤的直也一樣用`keyCode`就叫得出來囉！

雖然這其實有人帶的話簡單的要命，但有時候真的鬼打牆卡住就真的是卡很久XD，今天真的是有撞牆到啦，但搞定就是很開心！

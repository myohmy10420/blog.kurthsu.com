---
title: '[JavaScript]使用毫秒簡單的製作無限迴圈'
date: 2016-10-15 04:50:00
categories:
 - [生活, 跨行學程式]
 - [JavaScript]
tags:
 - JavaScript
---
最近照著教學做了一個類似小蜜蜂那種Shooting game的遊戲，其中當然需要一個主要迴圈不斷的重複啟動達到畫面一直刷新的效果，當然有很多種做法可以達到無限迴圈，但我來分享一下教學上使用秒數做出來的無限迴圈吧！

## new data().getTime()的配合
首先先瞭解一下new data()，顧名思是就是列出自身電腦為基礎的切確年月日時間，以下面簡單程式碼為例：
``` javascript
var d = new Date();
console.log(d);  //顯示 Mon Oct 15 2016 12:53:53 GMT+0800 (CST)
```
那getTime是做什麼的呢?
getTime可以算出當前物件時間距離1970 年 1 月 1 日午夜的毫秒數，所以new data().getTime就可以得出現在以自身電腦為基礎的時間距離1970 年 1 月 1 日午夜所差別的毫秒數，簡單的範例可以參考[W3school的這篇](http://www.w3schools.com/jsref/tryit.asp?filename=tryjsref_gettime)。

``` javascript
function loop () {
	if (new Date().getTime() - lastLoopRun > 40) {
		function1();
		function2();
		function3();
		functioni();

		lastLoopRun = new Date().getTime();
	}
  //這樣看起來雖然因下面setTime讓loop每2毫秒就重啟一次，但其實if裡面所有的function是約40毫秒
  //才會達成條件而全部刷新一遍！

	setTimeout('loop();', 2); //每兩毫秒又啟動自己這個迴圈形成無限迴圈
}

loop(); //別忘了要下指令去第一次強制啟動function loop()
```

無限迴圈的實作方法一定還有很多種，但這種也是簡單的方式之一。

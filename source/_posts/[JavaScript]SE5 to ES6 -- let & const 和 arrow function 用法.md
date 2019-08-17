---
title: '[JavaScript]SE5 to ES6 -- let & const 和 arrow function 用法'
date: 2016-10-27 14:14:00
categories:
 - [生活, 跨行學程式]
tags:
 - JavaScript
---
在學習使用React的路上我相信八成以上的教學都會建議使用ES6的語法，分享一下剛剛學到的arrow function和var的轉變。
## ES5的var為什麼轉換為ES6寫法不同？要如何修改？
以下是一段簡單的ES5語法的function
``` javascript
var App = function() {
	return (
		//somecode...
	);
};
```

製作變數`var`的指令在以前最原始的JavaScript有一個潛規則，如果此變數會跟著之後程式碼計算而變動則命名原則為很簡單的小寫，例如：
``` javascript
var numbers = 10;
numbers = 10 + 2; //此時numbers被計算為12了
//這裡的命名方式很簡單就為小寫
```
如果變數為之後都不會再變動則命名全部為大寫：
``` javascript
var MESSAGE = "Hello World!";
console.log(MESSAGE + "Kurt!");//此時輸出Hello World!Kurt!
//這裡的名命方式為全部大寫
```
當然這是沒有硬性規定的，不照著這麼做程式照樣也可以跑，但程式碼是要給大家容易維護的，所以這種潛規則命名方式就讓別人很清楚這個變數不能隨意修改。

今天ES6的開發覺得這個規則非常人性化也非常實用，於是他自訂了另一種指令把這個規則發揚光大。
`let numbers = 10;`可變變數的指令var改為`let`
`const MESSAGE = "Hello World!";`不可變變數指令var改為`const`

把今天的範例修改後如下：
``` javascript
const App = function() {
	return (
		//somecode...
	);
};
```

## Arrow function
ES6一直想把程式碼改得越來越讓人易讀，也就是語意化，所以ES6有一種叫Arrow function的用法。

使用時機為一個物件(function)裡面的東西再也不會修改的時候，此時的宣告也必須使用const，以一開始的範例修改後如下：

``` javascript
const App = () => (
		//somecode...
	);
```
這樣子是不是一眼就可以知道這個物件是在做什麼事情了呢？
會稱為Arrow function的緣故就是因為`=>`這個符號很像是箭頭(Arrow)囉！

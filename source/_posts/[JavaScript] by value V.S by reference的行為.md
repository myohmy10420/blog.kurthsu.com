---
title: '[JavaScript] by value V.S by reference的行為'
date: 2017-04-26 12:47:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - by value
 - by reference
---
在JS的其中一個重要觀念是當創造一個變數`var a;`他會佔據一個記憶體位置，而當賦予給他值的時候`a = 1;`會把`a`的記憶體位置拷貝並且指向這個`純質(1)`。

這觀念或許很抽象，白話來講創造變數的時候他會是一個空白的記憶體，當你賦予它值`var a = 1;`的時候他會去拷貝`1`這個純質。

那什麼是by value和by reference呢？我們先來看最簡單的by value。

## by value
>只要給變數`純質(primitive)`，例如數值、字串、布林值等`非物件`的東西會產生`by value`這個動作。

我們直接看看例子：
``` javascript
var a = 1;
var b = a;
a = 2;
console.log(a);  //print 2
console.log(b);  //print 1
```
答案非常的直覺，但底下發生了什麼事情？

首先我們的程式碼會先產生Hoisting(創造與提升)這個動作，讓`a`和`b`各自有一個記憶體空間，賦予`a`等於純質`1`，記憶體拷貝並指向了這個純質。

再來`b = a;`的時候因為`a`的記憶體剛剛拷貝和指向了一個純質，所以`b`的記憶體也拷貝並指向了相同的`純質`，這時候！！雖然`a`和`b`的記憶體指向完全一模一樣的`純質`，但由於`by value`的行為其實`a和b的記憶體位置是不一樣的`！！！

白話譬喻的話就是`a買了一棟信義區的房子`，此時`b說要買跟a一樣的房子`，所以他們都有一模一樣在信義區的房子，但是`地址是不一樣的`，這就是`by value`的行為，非常好懂，再來我們看看by reference。

## by reference
>只要給物件(function算是物件)的東西會產生`by reference`這個動作。

再來直接參考下面的例子：
```javascript
var a = { greeting: 'Hi'};
var b = a;
a.greeting = 'Bye';
console.log(a);  //print Object { greeting: "Bye" }
console.log(b);  //print Object { greeting: "Bye" }
```
直接看結果我們發現`b竟然跟a直接同化了`！！發生了什麼事情？

比照剛剛`by value`的行為即使讓`b`直接等於`a`取得一模一樣的`純質`，但是`b`的`記憶體位置是一個新的位置`！

相反的`by reference`的行為並不會給`b一個新的記憶體位置`，所以直接變動`a`或者是`b`物件裡面的東西它們都會一起受影響，因為他們就是住在同一個屋簷底下。

#### 想同的規則即使是在function裡面的參數(parameter)也是一樣
直接看例子：
```javascript
var a = { greeting: 'Hi'};
var b = a;
a.greeting = 'Bye';
console.log(a);  //print Object { greeting: "Bye" }
console.log(b);  //print Object { greeting: "Bye" }
function changeName (obj) {
  obj.greeting = 'morning';
}
changeName(b);
console.log(a);  //print Object { greeting: "morning" }
console.log(b);  //print Object { greeting: "morning" }
```
傳入changeName的參數會改動參數裡`greeting`的值變為`morning`，由於`by reference`的行為`a`也被我同步了！

## 如果是用物件實體(object literal)的方式指定物件的值，那麼會是by value
直接看例子：
```javascript
var a = { greeting: 'Hi'};
var b = a;
b = { greeting:'Bye' };
console.log(a);  //print Object { greeting: "Hi" }
console.log(b);  //print Object { greeting: "Bye" }
```
結果不會同步！！

因為`b = { greeting:'Bye' };`這行等同於`b自己創造了一了新的物件`，所以`b`和`a`已經不是共用同一個記憶體位置了，簡單的說`b`已經不和`a`住在同一個屋簷下，`b`自己買了新房子了！

如果是用`b.greeting = 'Bye';`這種方法是更改物件裡面的內容，也就是還在同一個屋簷下但是做了裝潢，當然兩個同居的人就會享有一樣的效果，這就是`by value`和`by reference`的行為。

了解了這個在規劃的時候要盡量避免踩上這些地雷，所以在操縱變數的時候要注意一下這些觀念，不然這算是非常難degug的東西，尤其在angular的`$scope`運用會死得很慘XD。

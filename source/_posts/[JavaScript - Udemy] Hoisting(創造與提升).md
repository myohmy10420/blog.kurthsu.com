---
title: '[JavaScript - Udemy] Hoisting(創造與提升)'
date: 2017-03-29 08:01:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - Hoisting
---
之前在[Udemy](https://www.udemy.com/courses/)的平台上趁特價買了[JavaScript全攻略：克服JS的奇怪部分](https://www.udemy.com/javascriptjs/learn/v4/overview)的中文課程，似乎是直接翻譯[JavaScript: Understanding the Weird Parts](https://www.udemy.com/understand-javascript/)的英文課程，在英文課程裡使用者回饋的評價算是很不錯的。

#Hoisting(創造與提升)
>這個階段是Javascript在初始化的時候會執行的動作。
>當Javascript在一行一行執行程式碼以前，會先空出記憶體把所有變數和函示創造出來，但是先沒有放值進去。

我們直接看看範例(console.log後有斜線註解的地方代表有執行成功並寫出結果)：
```javascript
var a = 'Hi!';
function b () {
    console.log('Is b!'); //"Is b!"
}
console.log(a); //"Hi!"
b();
```
到這邊不難理解Js從第一行執行到最後一行會發生什麼事情以及他的特性，那如果把程式碼換個位置呢？
```javascript
console.log(a); //undefined
b();
var a = 'Hi!';
function b () {
    console.log('Is b!'); //"Is b!"
}
```
第一行的`console.log(a)`並不是print出`"Hi!"`而是`undefined`，這是因為Javascript在初始化`只會先空出記憶體去創造變數和函示`，直到執行到給予該變數(例如：var a = 'Hi!';)。

以我們人類的看得懂的程式碼角度來看，Javascript在Hoisting的階段做了下列事情：
```javascript
var a;
function b () {
    console.log('Is b!');
}
```
但如果我們沒有先創造創造變數的話Javascript就會報錯囉！
例如改變剛剛的例子把`var a = 'Hi!';`這行拿掉：
```javascript
console.log(a);
//錯誤：Uncaught ReferenceError: a is not defined
b();
function b () {
    console.log('Is b!');
}
```
##執行環境
再來要順便淺談一下執行環境問題，Javascript的Hoisting是在該環境唷！譬如剛剛的程式碼是在全域環境，此時Hoisting在全域環境的有本來就有的`window`和`this`之外有自己創的`變數a`和`函示b`，如果創造的變數不是在全域環境而是在函示裡面呢？(順帶一提現在開始是我個人推測、測試與驗證XD)
```javascript
console.log(a); //undefined
b();
console.log(c);
//錯誤：Uncaught ReferenceError: a is not defined
var a = 'Hi!';
function b () {
    var c = 'Yo!';
    console.log('Is b!'); //"Is b!"
}
```
此時在執行`console.log(c);`時又報錯了因為`全域與函示是不同的環境`，所以在Hoisting的時候找不到`變數c`可以創造，所以程式碼再改寫一下：
```javascript
b(); //"Is b!"
function b () {
    console.log(c); //undefined
    var c = 'Yo!';
    console.log(c); //"Yo!"
    console.log('Is b!'); //"Is b!"
}
```
我們發現函示裡面Hoisting運行的方式跟全域的方式是一樣的，那我們來做個執行順序的統整吧！
```javascript
console.log(a); //undefined
b();
var a = 'Hi!';
console.log(a); //"Hi!"
function b () {
    var c = 'Yo!';
    console.log(c); //"Yo!"
    console.log('Is b!'); //"Is b!"
}
```
Hoisting沒問題之後再依造Javascript一行一行執行程式碼的特性，我們得出在console裡print出的順序為：`undefined > "Yo!" > "Is b!" > "Hi!"`

執行環境的地方是個人測試與淺見，如有觀念錯誤或更好的解釋方法歡迎留言糾正一下囉！

---
title: '[JavaScript] split 分割字串'
date: 2017-03-22 02:08:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - split
---
# split(分割字串)
Syntax(語法)：
``` javascript
str.split([separator[, limit]]);
```
參考文章：[Mozilla Developer Network(MDN)](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/String/split)


### 字串分割完會變成陣列
直接看範例：
``` javascript
var stringToSplit = 'apple and banana are my favorite fruits.';
var arrayAfterSplit = stringToSplit.split(' ');
console.log(arrayAfterSplit); //["apple", "and", "banana", "are", "my", "favorite", "fruits."]
```
這時候要取得哪一個想要的字串只需要使用`separator[index]`就行囉！

當然我們可以分割再分割
``` javascript
var stringToSplit = "?id=12345678&passwd=00000000";
//在這裡我只要12345678這個字串時要做兩次分割
var arrayAfterSplit = stringToSplit.split('=');
console.log(arrayAfterSplit); //["?id", "12345678&passwd", "00000000"]
//這時候做第二次切割
var theIdNumber = arrayAfterSplit[1].split('&')[0];
console.log(theIdNumber); //"12345678"
```

### 選擇切出幾塊字串
使用方法：
`split(separator, number);`
直接看範例：
``` javascript
var stringToSplit = 'apple and banana are my favorite fruits.';
var arrayAfterSplit = stringToSplit.split(' ', 3); //只取三塊
console.log(arrayAfterSplit); //["apple", "and", "banana"]
```

### 搭配.join()更換字元

在MDN的範例裡面我發現有趣的東西是搭配.join()可以做到更換字元的效果：
``` javascript
var stringToSplit = 'apple and banana are my favorite fruits.';
var arrayAfterSplit = stringToSplit.split(' ').join('/');
console.log(arrayAfterSplit); //"apple/and/banana/are/my/favorite/fruits."
```
`要注意這裡console.log出來的不是陣列又變回字串了！`
join裡的東西會自動填入剛剛split的切割位置。

#### 小記
會遇到這個問題是因為我遇到要利用網址(URL)取得使用者的id，這樣讓使用者在點進去網址的時候可以利用這個id得到他的資料，所以會搭配到`var currentLocation = window.location;`這個方法，例如網址為：
`https://somecompany.com/somepage?id=123456`
此時一進去這個連結我要用123456這個id去接取api初始化屬於該使用者的頁面資料：
``` javascript
var currentLocation = window.location;
console.log(currentLocation); //https://somecompany.com/somepage?id=123456
var arrayAfterSplit = stringToSplit.split('=');
console.log(arrayAfterSplit[1]); //"123456"
```
這樣就可以取得id囉，當然`window.location`取得的狀況有很多種所以一開始一定要先`console.log`下來看一下。

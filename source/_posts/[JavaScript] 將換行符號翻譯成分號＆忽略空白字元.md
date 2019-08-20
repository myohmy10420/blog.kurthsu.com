---
title: '[JavaScript] 將換行符號翻譯成分號＆忽略空白字元'
date: 2017-04-29 08:30:00
categories:
 - [JavaScript]
tags:
 - JavaScript
---
##將換行符號翻譯成分號
>養成開頭大括號跟要做的事情擺在同一行的習慣

不管在哪種編譯器或者可以打字的地方，其實當我們換行的時候電腦是會key上一個我們看不見得符號(這邊用↓當代表)。

但要小心`有時候JS會把換行符號↓自動轉變成;`，所以我們自己先養成一個好習慣避免這種很難debug的錯誤。

直接看例子：
``` javascript
var a = true;
if (a) {
    console.log('Run without ↓');
}

if (a)
{
    console.log('Run with ↓');
}
```
你能猜到哪個會執行嗎？其實兩個都會XD，那換一個呢？
``` javascript
function noChange () {
    return {
        status: "I don't have ↓"
    }
}

function hasChange () {
    return
    {
        status: "I have ↓"
    }
}

console.log(noChange());
console.log(hasChange());
```
我們會發現第一個return正確的物件，第二個卻return undefined，這是因為`retrun後面接上換行符號JS的引擎會自動補上;`的關係，所以剛剛的hasChange其實會被翻譯成這樣：
``` javascript
function hasChange () {
    return;
    {
        status: "I have ↓"
    }
}
```

但在大多數的情況下是沒關係的，例如function、迴圈、甚至在物件或陣列裡面等等，但為了避免這種小又難以解決的錯誤，我們就`養成開頭大括號跟要做的事情擺在同一行的習慣`吧！

##忽略空白字元
JS會忽略空白字元，或使用tab產生出來的空白，`但全形的空白不會忽略`(不過這個只有在中文輸入法才比較會碰到)。

其實下面的程式碼是可以正常執行的：
``` javascript
var a,
    b,

    //this is a pen

    pen;

a = 1;
b = 2;
pen = // I give it a color
    'green';

console.log(a);
console.log(b);
console.log(pen);
```
其實這是會正常執行的哦！！所以看到別人的專案註解或換行一大堆不用慌，也是看準關鍵字然後保持這個原則就ok了！

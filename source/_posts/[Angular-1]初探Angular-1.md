---
title: '[Angular-1]初探Angular-1'
date: 2016-11-18 07:50:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
 - Angular1
---
最近開始研究Angular1的框架，參考的是code school的[Shaping up with angular.js](https://www.codeschool.com/courses/shaping-up-with-angular-js)，裡面的教學引導性超高，不排斥英文的人要學習Angular1的話非常推這個管道！他們還為這個教學做了主題曲XD。

第二個管道Angular的[官方網站](https://angularjs.org/)教學也做得不錯，但教材有點舊，而且他們也很推code shcool的教學。

p.s:Angular現在有出2，但玩法跟1差蠻多的而且還算在測試階段，有興趣的就去另外找資源哦！

## 基本認識
> ###Directive:
Angular特有的標籤，`ng-app`，`ng-controller`等等的標籤時常綁在html的DOM上面。
> ###Expression:
Angular特有的物件用法，所有的東西可以用雙括號`{ { } }`包住，例如直接在HTML輸入This is {{4 + 6}}後，pring出來的訊息是This is 10。

## 簡單架設環境
> 用簡易的CDN帶入Angular套件和最常搭配的Boostrap樣式即可打簡單的code囉！
``` html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap-theme.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.8/angular.min.js"></script>
```

## 取之模組用之模組
> Angular的世界通常是在JS檔案裡面自創模組後插入HTML的DOM裡面互動。

以下一個簡單的範例：
``` javascript app.js
var app = angular.module("gemStore", []);
```

``` html app.html
<!DOCTYPE html>
<html ng-app="gemStore">
  <head>
    <link rel="stylesheet" type="text/css" href="bootstrap.min.css" />
    <script type="text/javascript" src="angular.min.js"></script>
    <script type="text/javascript" src="app.js"></script>
  </head>
  <body>
    <h1>{{"Hello, Angular!"}}</h1>
  </body>
</html>
```

應該可以看到pring出簡單的Hello, Angular!

參考來源：[Shaping up with angular.js](https://www.codeschool.com/courses/shaping-up-with-angular-js)

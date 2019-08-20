---
title: '[Angular-1]$http post formdata基本使用'
date: 2017-05-16 10:07:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
 - $http
 - formdata
---
我的工作開發框架是使用Angular1，在串接API的時候$http讓我們方便許多。

首先原生的Ajax使用可以[參考這篇MDN](https://developer.mozilla.org/zh-TW/docs/Web/API/FormData/Using_FormData_Objects)，那這篇紀錄一下怎麼用$http上傳formdata的東西：

``` javascript
var dataObj = someObj;
console.log(dataObj); //要上傳的東西，上傳前看一下是好習慣。
var formdata = new FormData();
angular.forEach(dataObj, function (value, key) {
    formdata.append(key, value);
});

$http({
    method: "POST",
    url: "URL",
    data: formdata,
    headers: { "Content-Type": undefined }
}).then(function (response) {console.log(response)})
  .catch(function (response) {console.log(response)});
```
其實最需要注意的是`headers : { "Content-Type": undefined }`這裡的設定，查到很多舊資料是設定false，但問過一些人之後似乎是有一次angular的改版要變設定為undefined。

第二個設定是有人把`data: formdata`使用一個$parse過濾要上傳的東西，變成`data: $parse(formdata)`，但我沒有成功過，那似乎是翻譯angular的語法，已經有點不相干了。

以正常的物件post基本formdata的時候就使用上面那些設定就好囉！有特殊的使用就要再去了解了`headers`要如何設定。

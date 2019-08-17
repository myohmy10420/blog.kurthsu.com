---
title: '[Angular-1]ng-repeat'
date: 2016-11-18 10:05:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
 - Angular1
---
當陣列裡面有很多物件要怎麼讓程式自己一筆一筆撈出來呢？

``` javascript app.js
var app = angular.module('gemStore', []);

app.controller('StoreController', function(){
  this.products = gems;
//命名上複數加S會讓可讀性更佳
});

var gems = [
  { name: 'Azurite', price: 2.95 },
  { name: 'Bloodstone', price: 5.95 },
  { name: 'Zircon', price: 3.95 }
];
```
使用ng-repeat實作在HTML的DOM裡面
``` html app.html
<div class="product row" ng-repeat="product in store.products">
//命名product一筆一筆去撈store.products裡陣列裡的每一個物件
	<h3>
    {{product.name}}
    <em class="pull-right">${{product.price}}</em>
//呼叫剛剛命名的product裡的每一個物件的東西
  </h3>
</div>
```

應該會把gems裡每一個物件的name和price一行一行全部列出來。

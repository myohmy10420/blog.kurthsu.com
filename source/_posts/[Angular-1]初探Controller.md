---
title: '[Angular-1]初探Controller'
date: 2016-11-18 09:05:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
 - Angular1
---
Controller是操縱功能的Directive，包含在Module之下，在JS創造的方式也與function十分相似。

實作範例：
``` javascript app.js
var gem = { name: 'Azurite', price: 2.95 };
//創造一個物件
var app = angular.module('gemStore', []);
app.controller("StoreController", function(){
  this.product = gem;
  //把物件帶入this.product
});
//因為我們創造的模組名稱是app，所以要用app.controller創建
```

``` html app.html
<body ng-controller="StoreController as store">
//把controller導入body的DOM並給他名稱store
  <div class="product row">
    <h3>
      {{store.product.name}}
      <em class="pull-right">{{store.product.price}}</em>
//之後要拿這個controller的東西要用store.product去拿
    </h3>
  </div>
</body>
```

正常來說就會顯示gem裡面的name和price囉！
直得一提的是這個Controller裡的東西只有在這個DOM裡面使用！！

錯誤的範例如下：
``` javascript
<div ng-controller="StoreController as store">
</div>
//Controller使用的DOM已經結束了！！
<div>
  <h3>
    {{store.product.name}}
    <em class="pull-right">{{store.product.price}}</em>
  </h3>
</div>
//這時候什麼東西都抓不到囉！
```

以上是基本Module裡的Controller與HTML溝通，跟原生JavaScript的function使用方法一樣，裡面也可以放物件和功能，但他一定要有主人(Module)帶領他唷！

參考上一篇{% post_link '[Angular-1]初探Angular-1' '[Angular-1]初探Angular-1]' %}的文章，教學影片給我們Angular的小總結，如下附圖：
![螢幕快照 2016-11-18 下午6.18.38.png](http://user-image.logdown.io/user/18810/blog/18323/post/1117465/cfR9j8sJTESQRi2bvmDQ_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202016-11-18%20%E4%B8%8B%E5%8D%886.18.38.png)

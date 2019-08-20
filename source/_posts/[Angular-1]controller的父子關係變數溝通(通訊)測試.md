---
title: '[Angular-1]controller的父子關係變數溝通(通訊)測試'
date: 2017-05-11 02:23:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
 - controller
---
最近在重整公司專案一個controller一個頁面吃到完的hard code，網路上找了許多規範，大家都很推[johnpapa](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#controllers)的angular規範，但在`父子關係的controller之間的通訊是如何呢`？

在這邊紀錄一下我直接用實測的方式做出來，還不是很清楚底層發生了什麼事情。

## 父子都注入$scope的話
在這邊我就不多贅述創造module的部分，只針對controller寫範例：
``` html
<div ng-controller="parentCtrl">
    {{ father }}
    <div ng-controller="childCtrl">
        {{ son }}
    </div>
</div>
```
這是我公司最一開始html的寫法，我想也是大家剛接觸angular的寫法，我來看看JS的部分(參照[johnpapa](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#controllers)的規範寫兩個JS檔案)：
``` javascript parentCtrl.js
angular
    .module('app')
    .controller('parentCtrl', parentCtrl)

    parentCtrl.$inject = ['$scope'];

   function parentCtrl ($scope) {
        $scope.father = "hasFather";
        $scope.son = "hasSon";
   }
```
接下來創造childCtrl，`裡面沒有任何的程式碼`，純粹console.log變數出來看：
``` javascript childCtrl.js
angular
    .module('app')
    .controller('childCtrl', childCtrl)

    childCtrl.$inject = ['$scope'];

   function childCtrl ($scope) {
        console.log($scope.father);//hasFather
        console.log($scope.son);//hasSon
   }
```
發現`在childCtrl其實就跟parentCtrl共用$scope`了！
更明顯的是可以直接在html裡面看到結果，這讓我迷思的一點是大家都說`每一個controller都有屬於自己的$scope，但在父子關係的controller似乎不是這樣子`。

##父子使用this去注入$scope的話
在剛剛的例子之後，接下來我們親愛的[johnpapa](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#controllers)說不要這樣寫，因為html和變數一多會很難以維護，意思就是`不要一直用$scope宣告變數`，那該怎麼讓變數跟自己的controller綁定？

有一版的`angular新增了controller as ctrl的這個功能`，現在我們可以很直覺的直接在html裡面看得出這個變數是屬於哪個controller的。

來看看html做了哪些更新：
``` html
<div ng-controller="parentCtrl as parent">
    {{ parent.father }}
    <div ng-controller="childCtrl as child">
        {{ child.son }}
    </div>
</div>
```
使用`controller as`之後表達式一定要這樣寫才行喔！！
這樣就`很清楚知道這個表達式的變數是哪個controller裡面`的。

那麼controller怎麼使用[johnpapa](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#controllers)所說的this呢？
``` javascript parentCtrl.js
angular
    .module('app')
    .controller('parentCtrl', parentCtrl)

    parentCtrl.$inject = ['$scope'];

   function parentCtrl ($scope) {
        var self = this;
        this.father = "hasFather";
   }
```
childCtrl也要有自己的變數囉！但是我們依然可以測試一下childCtrl到底吃不吃得到parentCtrl的變數，我們用console.log來查查：
``` javascript childCtrl.js
angular
    .module('app')
    .controller('childCtrl', childCtrl)

    childCtrl.$inject = ['$scope'];

   function childCtrl ($scope) {
        var self = this;
        this.son = "hasSon";
        console.log(self.father);//undefiend
   }
```
此時html都可以正常顯示的，因為parent.father有正確的在parentCtrl裡宣告，而child.son有正確的在childCtrl裡宣告，但是在`childCtrl裡已經找不到self.father的變數`囉！

這邊還蠻直覺的，因為畢竟在兩個controller裡面都宣告自己的this，我們也可以`在兩個環境都下console.log(this);就會看出他們確實指向不同的物件(有不同的變數環境)`。

### 接下來最有趣的東西來了
如果我們在childCtrl把var self = this;拿走呢？猜猜看會發生什麼事？
``` javascript childCtrl.js
angular
    .module('app')
    .controller('childCtrl', childCtrl)

    childCtrl.$inject = ['$scope'];

   function childCtrl ($scope) {
        this.son = "hasSon";
        console.log(self);//會發生什麼事情！？
   }
```
我在下console.log(self);以前會認為因為在childCtrl找不到self所以向外看到了parentCtrl有self，所以應該會指向他，但是.....BAAAANG!!!

如果`沒有宣告self的話他竟然指向了window`！！！
ＷＴＦ？？
self不是被我宣告是指向parentCtrl這個物件裡嗎XD？

總之測試告一段落，因為我本來一直苦惱著要怎麼讓兩個controller溝通，網路上查就有三大種方法，第一個自己`創service`，第二個是使用`$broadcast的事件`，再來第三個是直接使用`$rootScope`，在這裡就不在贅述囉！

這是我自己測試出來的，如果大家有什麼好心得可以交流一下囉！

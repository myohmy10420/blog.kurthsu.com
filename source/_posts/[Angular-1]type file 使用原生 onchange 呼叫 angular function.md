---
title: '[Angular-1]type file 使用原生 onchange 呼叫 angular function'
date: 2017-05-16 09:33:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
---
我的工作是以Angular1框架做開發，遇到了需要上傳檔案的問題。

基本的方法查詢後想說以：
`<input type="file" ng-change="upload(this)">`
這行程式碼做開發，但發現ng-change並不會被觸發，只能用比較特別的方法。

## 使用原生onchange代替ng-change
那怎麼用原生的onchange呼叫到angular的controller裡的function呢？
直接看範例：
``` html
<div ng-controller="uploadController">
    <input type="file" onchange="angular.element(this).scope().upload(this)" >
</div>
```
這樣就可以觸發囉！
再來怎麼抓上傳檔案的內容呢？在controller裡的function裡下：
``` javascript
function upload (element) {
    console.log(element.files[0]);
}
```
就可以開始對他進行操作了。

## 如何呼喚在controller as Ctrl的function?
有參考過John papa的規範一定很熟悉為了程式碼的乾淨會在html下：
``` html
<div ng-controller="uploadController as upload">
    <div ng-click="upload.someFn()">click me!</div>
</div>
```
這樣可以馬上知道someFn()這個function是誰家的controller，那融合剛剛說的用原生的onchange怎麼做到冠上Controller名稱的function呢？
直接看範例：
``` html
<div ng-controller="uploadController as uploadCtrl">
    <input type="file" onchange="angular.element(this).scope().uploadCtrl.upload(this)" >
</div>
```
這樣controller裡的程式碼就能繼續照著規範走保持整潔囉！

底層怎麼走的還不太清楚，小小的工作心得分享！

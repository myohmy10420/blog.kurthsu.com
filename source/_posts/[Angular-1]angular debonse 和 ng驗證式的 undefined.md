---
title: '[Angular-1]angular debonse 和 ng驗證式的 undefined'
date: 2017-07-07 07:43:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
 - debonse
 - model
---
遇到一種情況，想要在html用debonse延遲ng-change去判斷該model一些東西。

但其實有個小地雷是`當model沒有通過html的angular驗證式的話model都會是undefined`唷！！

我們可以直接先看到[官方網站的範例](https://docs.angularjs.org/api/ng/directive/ngPattern)，他裡面的條件設置為只能輸入數字，可以發現到當輸入的不是任意數字時input valid?會等於false之外，`model是會顯示不出來的哦，因為此時的model會是undefined`！

所以如果要設計一個model同時搭配ng-parttern和ng事件的話絕對要注意`model沒通過ng-parttern的話永遠會是undefined！`

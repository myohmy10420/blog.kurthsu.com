---
title: '[Angular-1]自製驗證式ng-pattern'
date: 2016-12-18 10:08:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
 - Angular1
---
其實Angular的其中一個精華是已經可以在HTML裡面做很多事情和判斷，也就是說Angular可以寫到幾乎已經不用寫太多JS了，但這是一個雙面刃，畢竟JS是前端很重要的命根之一。

或說回來，Angular在製作表單可以把引導性做的很高，立即的判斷該欄位是否是正確的輸入值(例如電子信箱不會有中文字)，許多內建基本的驗證式(例如信箱格式，文字長短等等)上網都可以輕鬆找尋到就不多介紹。

這次的主題是可以自定義條件的`ng-pattern`，那想要善用`ng-pattern`就不得不學習一下燒翻腦袋的`正則表示式(RegExp)`啦！正則表示式就請參考我的另外一篇文章囉！

## ng-pattern的用法
先來討論ng-pattern使用方式，最基本的使用方式是把他放入一個input的tag裡面，其實就跟表達式(ng-show等等)的方法一樣，但只有他單獨出現的話是沒有意義的，簡單的例子如下：
``` html
<div ng-form name="demoform">
	<input type="text"
	name="identify"
	placeholder="請輸入身分證"
	class="style"
	ng-model="user.identify"
	ng-pattern="/[A-Z][0-9]{9}/" />
	<span ng-show="demoform.identify.$error.pattern">輸入的格式不正確</span>
</div>
```
第一個規則是驗證式(如這次的例子ng-pattern)都`需要被包在form的tag裡面`喔！

`Angular有自己的ng-form`，與原生的HTML的form差異在哪這邊不多做探討，總之直接用一套Angular的表達式也比較有優點，不要忘記幫ng-form取上名字`name="demoform"`。

input的原聲屬性全部都可以使用，ng-model是Angular處理資歷的表達式不多做探討，ng-pattern為驗證式，當然不能忘記要幫他取名子`name="identify"`。

再來先不要探討ng-pattern裡面的東西，ng-pattern本身是驗證式，所以當使用者輸入的欄位不符合我自訂的條件(/[A-Z][0-9]{9}/)的話會激活一個內建$error的屬性，所以我`span的表達式ng-show會為true而顯示`。

這裡可以很語意化的看成`表單(demoform)的(.)某個input(identify)的(.)錯誤($error)的(.)某個驗證式(pattern)為真`，所以span這個tag就會出現輸入的格式不正確這行字。

這就是驗證式基本的用法，當然可以配上CSS和其他手法玩得讓表單更有引導性，但這裡就不在深入討論了，至於基本的驗證式介紹也可以直接[參考Angular的官方網站](https://docs.angularjs.org/api/ng/directive/input)。

#### ng-pattern可以外部帶入
直接看範例：
```html index.html
<input type="text"
	name="identify"
	placeholder="請輸入身分證"
	class="style"
	ng-model="user.identify"
	ng-pattern="identifyRegExp" />
```
```javascript app.js
angular.module('app',[])
	.controller('contrl',[finction(){
		this.identifyRegExp = /[A-Z][0-9]{9}/;
	}])
```

當然也可以自己製作一些function讓他動態產生，這裡就不多討論了，接下來正則表示式就請參考我的另外一篇文章囉！

---
title: '[Angular-1]ng-class問題紀錄1'
date: 2017-03-20 03:30:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
 - ng-class
---
ng-class
這次我遇到的情況是想再controller控制一個變數，讓ng-class去判斷這個變數的內容改變他的CSS，但是是用Jquery的Listener去觸發。

先參考一些我在網路上查到的用法：
[Angular官方網站](https://docs.angularjs.org/api/ng/directive/ngClass)
[Day22- 入門AngularJS筆記-AngularJS指令(19) ng-class](http://ithelp.ithome.com.tw/articles/10139571)

第一件事情是我想說ng-class如果成立的話能否直接蓋掉原本本的class:
設定html
``` html
<table>
  <tr>
    <td class="inputTd" ng-class="{'InputTooShort': tableInputTooShort}">
    	<input id="userInput" model="tableInput" />
    <td>
  </tr>
</table>
```

設定JS
``` javascript
$scope.tableInputTooShort = false;
$('#userInput').bind('focusout', function ($scope) {
	if(tableInput.length < 3) {
		$scope.tableInputTooShort = true;
	}else {
		$scope.tableInputTooShort = true;
	}
});
```

設定CSS
``` css
.inputTd {
   border: 1px solid #999;
}
.InputTooShort {
    border: 2px solid red !important;
}
```

此時出現很神奇的Bug，每當我輸入的字串小於三個字元並且滑鼠點出input去觸發他的focusout，$scope.tableInputTooShort這個變數會變成true沒錯，但是這個tag的class並沒有更新，反而是我再點入input輸入格隨意輸入換刪除一個字元他的class才會更新為InputTooShort這個CSS，反之，當他被InputTooShort這個CSS更新後我把字元輸入到三個以上後點出input讓focusout把$scope.tableInputTooShort這個變數會變成false時class依然沒有更新，也是要再我重新點回input刪除或輸入任意字元才會更新為inputTd，但這不是我要的效果，我希望的是觸發focusout之後，的CSS就應該要更新了。

起初以為是判斷式的關係，但我試了很多判斷式還是都一樣，目前功力太淺也還找不出原因，只好再直接求助Angular的驗證式：
設定html
```html
<table ng-form="form">
  <tr>
    <td class="inputTd" ng-class="form.pathDepartAddr$error.minlength ? 'InputTooShort' : 'inputTd'">
      <input id="userInput" model="tableInput"
      name="pathDepartAddr"
      ng-minlength="3" />
    <td>
  </tr>
</table>
```

其實這樣就直接也省下JS的程式碼了，雖然是成功有效果但我最初的希望會是點出格子外後才觸發更新CSS，但如果使用這個方法的話會比較像onchange一樣的效果，只要輸入或刪除任意字元就會一直觸發和刷新這個CSS。

form.pathDepartAddr$error.minlength可以參考我{% post_link '[Angular-1]自製驗證式ng-pattern' '之前文章' %}的解釋，值得注意的是雖然在input裡面我是寫ng-minlength="3"，但是在ng-class裡面只有寫minlength不用加上ng-這串。

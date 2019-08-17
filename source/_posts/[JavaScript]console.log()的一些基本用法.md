---
title: '[JavaScript]console.log()的一些基本用法'
date: 2016-09-13 05:52:00
categories:
 - [生活, 跨行學程式]
 - [JavaScript]
tags:
 - JavaScript
---
在上課的時候老師時常使用console.log()這個語法一直不斷去檢測許多東西，我一開始其實蠻不喜歡用也不知道怎麼用的，很大的原因之一是他使用起來有點自由，什麼東西都能查，也可以隨時使用，然而雖然在網路上也可以查到的他能做些什麼事情，但我想要的資訊是當我需要使用它時我該怎麼使用，那接下來我就針對遇到的一些問題稍微解釋一下我遇到的問題也順便為自己做筆記。

## 使用console.log()來查詢物件裡的東西

``` javascript
var fruit = [
    {apple},
    {banana},
    {watermelom}
    ]
```
這時候只要輸入 `console.log(fruit);` 就能列出fruit裡的東西，記得是要在檢查原始碼的console那邊看，除非你想多打一行把它印在html裡面，不過既然只是做查看和測試的動作應該是不需要這麼麻煩。

好，那這篇重頭戲是今天如果你要設計一個按鈕之類的要啟動一個function要如何知道有沒有成功？例如：
在html製作一個簡單的按鈕
``` html
<button onclick="catchNumber()" value="5">5</button>
```
在JavaScript製作簡單的function
``` javascript
function catchNumber () {
  console.log("123");
}
```
這時候打開網頁點選按鈕，打開檢查原始碼的console那裡有印出123就代表你做的按鈕是有成功啟動function的哦！
我當初很納悶，測試東西的方法百百種，為何一定要用console.log()去搞？我在function做一個alert("123")也行呀！

## console.log()可以查變數的底層屬性！

現在我們再做另一個有趣的小測試，今天有三個按鈕分別是5,10,15，他們要帶入同一個function，那我們怎麼知道有沒有成功帶入function，帶入function的變數是多少呢？

我們做一個新的html
``` html
<div id="numberButton">
  <button value="5">5</button>
  <button value="10">10</button>
  <button value="15">15</button>
</div>
```
還有一個新的JavaScript
``` javascript
(function() {
  var numberButton = document.getElementById("numberButton");
  numberButton.addEventListener("click", catchNumber, false);

  function catchNumber(event) {
    console.log(event);
    event = event.target.value;
    console.log(event);
  }
})();
```
這裡要善用 ` .getElementById ` 和 ` .addEventListener ` 的話要放在『閉包』形式的 ` (function(){})(); ` 裡唷！不然 ` .addEventListener ` 找不到 ` numberButton ` 的值。

我在 ` function catchNumber() ` 裡面放了兩個 ` console.log(event); ` ，因為JavaScript的程式特性是順序著從頭執行到尾巴，這樣我們可以看到變數event的變化。

這時候打開檢查程式碼到console裡，點選剛剛在html做出來的隨便一個數字按鈕，第一個console.log出來的event有包含許多元素，展開來後又可以在target:button裡的value找到我們按鈕給變數event的元素。

再來我們單獨把value值取出來所以我打了 ` event = event.target.value; ` 這行，這時候第二個 ` console.log(event); ` 就只會印出按鈕的value值囉！

善用這些基礎的技巧，我們可以在單做一個功能的途中不斷的測試哪個環節有沒有出錯，不然等一次做好一個功能但是卻跑不出來，JavaScript並不會很仔細的敘述是哪個東西帶錯或出錯囉！

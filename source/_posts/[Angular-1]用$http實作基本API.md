---
title: '[Angular-1]用$http實作基本API'
date: 2016-12-10 06:51:00
categories:
 - [Angular, Angular1]
tags:
 - Angular
 - Angular1
 - $http
 - API
---
基本串接API的一種方式是從一個網址接收伺服器給我們的物件，通常在網址裡會帶有變數讓我們可以選取指定的資料。

Angular在這部分有提供非常方便的套件為$http，只要遵守範例中的規則即可取得我們所需的資料。

### 取得基本json資料
最簡單的Get去獲取資料的範例如下：
``` javascript app.js
$http({
	method: 'GET',
	url: 網址(要為字串格式)
}).then(function (response) {
	var resultDate = response.date;
	Do something...
}, function (response) {
	var errorStatus = respinse.status;
	Do something...
});
```
我們假設server回傳的資料如下：
``` json
{
	"name": Kurt,
	"date": 2016/12,
	"address": Taiwan
}
```

在`.then`之後的第一個function代表成功獲取資料後要做的事情，而逗號`,`之後的function則是如果失敗的話要做的事情，那麼系統如何判斷此次API的獲取資料是成功還是失敗呢？

在http協議下他們有定義三位數的狀態碼，在我們開啟每一過網頁或者做每一件有關於資料處理的事情(其實就是有跟server端每次的往來)都會有這些狀態碼去分類跟server溝通後的結果狀況，最常見的莫非是代表著找不到網頁的404狀態了！

在這邊我們簡單的了解`2開頭的狀態碼代表成功，3或4開頭的狀態碼代表失敗`，完整的狀態碼資訊可以參考維基百科[HTTP協議狀態碼](https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)。

不論成功還失敗我們都會把結果一整包帶入一個變數裡面，在範例裡面我用`response`表示，成功的話現在變數`response`現在是跟server回傳的資料一模一樣囉，也就等同於：
``` javascript
response = {
	"name": Kurt,
	"date": 2016/12,
	"address": Taiwan
}
```
成功下載下來的東西我宣告一個變數resultDate去取得response裡"date"的東西，所以現在我們的resultDate如下：
``` javascript
resultDate = "2016/12"
```
要注意的是如果我們用這種方法取得物件裡的資料會是`字串`的屬性喔！！！
所以我才會用雙引號來做表示。

要把字串改為可以用的格式(ex:數字，日期，布林值等等)都需要另外程式碼處理。

最後如果失敗的話通常我會把錯誤的代碼抓出來，一樣宣告一個變數`var errorStatus = respinse.status;`，再利用這個狀態碼去做其他的事情，例如跟使用者講為什麼會出現這些狀態碼，不然一般的使用者看不懂遊覽器預設的status 404是什麼意思，我們可以自製訊息跟使用者說找不到此網頁或系統維護中之類的『人話』。

其實status不論失敗或成功都是會跟著傳過來的，只是成功的2開頭狀態碼對我們來說似乎沒有什麼用，我們基本上是忽略他，不過可以嘗試著利用`var errorStatus = respinse.status;`把他叫出來看看。

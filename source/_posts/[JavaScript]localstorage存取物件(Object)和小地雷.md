---
title: '[JavaScript]localstorage存取物件(Object)和小地雷'
date: 2017-07-09 13:25:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - localstorage
 - Object
---
工作上因為手機端的需求要直接在APP裡面開啟我這邊的html，但是在 \<a> 這個tag無法使用_blank這個動作，他還是會切換到下一個網址造成回上一頁之後剛剛表單內的東西都被清空了，所以決定使用原生就有的localstorage(或者sessionstorage)來解決這個問題。

網路上可以查到的localstorage和sessionstorage寫法不只有一種，這邊就直接用sessionstorage下列簡單的例子當範例：
``` javascript
if ( window.sessionStorage["say"] === undefined ) {
    window.sessionStorage["say"] = 'Hello storage';
} else {
    var words = window.sessionStorage["say"];
}
console.log(words);
//第一次進入網頁會print undefined
//第二次進入網頁會print 'Hello storage'
```
記得window.sessionStorage["say"]內的say是自己命名的任意名稱，不是JS裡的變數～

`但如果是有一堆變數要暫存的話，這時候就需要利用sessionStorage直接打包成一個物件暫存資料到cookie裡面`，因為我不會想寫20行sessionStorage去存取資料的XD。

但遇到了點問題：
``` javascript
var myInfos = {
    name: 'Kurt',
    age: 25,
    isHandsome: 'Not really',
    humor: true
}
if ( window.sessionStorage["profile"] === undefined ) {
    window.sessionStorage["profile"] = myInfos;
} else {
    var someBodyInfos = window.sessionStorage["profile"];
}
console.log(someBodyInfos);
//第一次進入網頁會print undefined
//第二次進入網頁會print '[Object Object]'
```
Yo~~~~照上面的例子難道我真的要為了一個有二十個變數的表單寫二十次sessionStorage了嗎？(搥地板)
別擔心，我們親愛的谷歌大神和stack overflow雙劍合併馬上解決了我的問題。

噹噹噹～`使用Json格式存取`！


``` javascript
var myInfos = {
    name: 'Kurt',
    age: 25,
    isHandsome: 'Not really',
    humor: true
}
if ( window.sessionStorage["profile"] === undefined ) {
    window.sessionStorage["profile"] = JSON.stringify(myInfos);
} else {
    var someBodyInfos = JSON.parse(window.sessionStorage["profile"]);
}
console.log(someBodyInfos);
//第一次進入網頁會print undefined
//第二次進入網頁會print
//{
//    name: 'Kurt',
//    age: 25,
//    isHandsome: 'Not really',
//    humor: true
//}
```
這樣就能利用localstorage或sessionStorage存取打包好的物件甚至陣列摟！

還有個小地雷講下～再開發時我們愛用的`無痕視窗是無法暫存資料到cookie`的唷！那時做好後嚇了一跳勒XD。

最後如果想知道localstorage和sessionStorage有什麼差別的話可以[參考這篇高級打字員的技術雲的部落格文章](https://dotblogs.com.tw/shadow/2014/07/10/145879)說明。

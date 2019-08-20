---
title: '[JSON]什麼是JSON？為什麼要使用？'
date: 2017-04-20 13:50:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - JSON
---
# JSON是什麼？
今天剛好看到之前買的線上教學影片克服JS奇怪的部分介紹到JSON，所以說要真正認識JSON就一定要認識Javascript了！
尤其JSON又是真的受Javascript而啟發，他的全名也就是(JavaScript Object Notation)。

### 因JS的物件實體用法而生
物件實體(Object literal)是什麼呢？
稍微認識JS的人都知道要創造一個物件最基本的程式碼是：
```javascript
var person = new Object();
person.name = "Kurt";
person.age = 18;
```
JS的引擎有物件實體的功能可以用下面這種方法直接做到跟上面一模一樣的事情：
```javascript
var person = {
    name: "Kurt",
    age: 18
}
```
那這跟JSON有什麼關係呢？
### 以前傳送物件很浪費效率
以XML為例子，如果我們做資料傳輸直接傳送person這個物件電腦會幫我們傳`類似`下面這種東西：
```
<object>
    <name>Kurt</name>
    <age>18</age>
</object>
```
但如果轉為JSON格式，電腦就只會傳送原本我們所打的JSON格式：
```json
'{
    "name": "Kurt",
    "age": 18
}'
```
傳送過去再使用JS原生的function做簡單的處理(JSON.stringify)就變回於本的物件了。
比對一下傳送的字元幾乎少了一半以上！！所以JSON格式才會這個的熱門運用！

用這個例子第一次這麼的清楚知道JSON的由來，真有趣ＸＤ！

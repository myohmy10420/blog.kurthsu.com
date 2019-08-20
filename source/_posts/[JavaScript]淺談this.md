---
title: '[JavaScript]淺談this'
date: 2017-01-23 15:12:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - this
---
這幾天從研究apply和call之類的函示，突然發現JavaScript的觀念差得可以，建構子、this等等方法十分陌生，這些東西在我工作其實使用的場合真的很少，加上現在框架和語法糖的發展讓很多事情語意化的很理所當然，但我認為站在前端的場合上面還是要多懂得一些JavaScript的精神和觀念。

這篇主要討論基本的this，建議可以先了解我前一篇的文章{% post_link '[JavaScript]初探建構子(Constructor)' '初探建構子' %}，雖然不懂也沒關係，但他們確實是息息相關。

# this指向於調用該函式之物件
這個經常看到卻又很模糊的東西是什麼呢？
有的人說是執行『此次』時的屬性，有人說他像免洗餐具每次都會像是重新使用，第二次使用的人會是拿到一副新碗筷跟上次使用的人是完全不一樣的東西，網路上我有找到一篇解釋得蠻好的文章。

他解釋為：`this指向於調用該函式之物件`。

簡單的公式就是`物件.函式()`，此函式裡有我們自創的this。
使用以上公式觀察下面例子：
``` javascript
var singer = {
	words: '摩擦！摩擦！',
	say: function(){ console.log(this.words); }
};
singer.say();  //"摩擦！摩擦！"

var singer = {
	words: '似魔鬼的步伐',
	say: function(){ console.log(this.words); }
};
singer.say();  //"似魔鬼的步伐"
```
所以函示接在哪個物件的後面this就是指向該物件的內容囉！！

這邊菜鳥提醒一下this一定是在『方法』裡面，我之前實在對類別和方法的用法感到很困惑，沒想到還是工作兩個月後才有顯著的理解XD，詳情可以參考上一篇{% post_link '[JavaScript]初探建構子(Constructor)' '初探建構子' %}。

再來當函式前面沒有接物件的話，`this會指向全域物件`。
像這樣子獨立出現：`函式`。
``` javascript
var words = '原來你是我最想留住的幸運';
var sing = function () {
    console.log(this.words);
}
sing();  //'原來你是我最想留住的幸運'
```
當sing()這個函式前面沒有接著物件的話程式會判斷是接取最外面的東西。

接下來比較常見的小問題
``` javascript
var words = '你存在我深深的腦海裡';
var obj = {
	words: '我心裡有針要喊煞嘸聲',
	sing: function(){
		console.log(this.words);
		var sing = function(){
		  console.log(this.words);
		}
		sing(); //'你存在我深深的腦海裡'
	}
};
obj.sing();  //'我心裡有針要喊煞嘸聲'
```
主要都是看接什麼物件而唱出什麼歌詞，那有一個有趣的玩法可以讓this保留下來。

延續上面的例子：
``` javascript
var words = '你存在我深深的腦海裡';
var obj = {
	words: '我心裡有針要喊煞嘸聲',
	sing: function(){
		console.log(this.words);
		var that = this;  //我把這個this存到變數that裡面了！
		console.log(that);  //出現完整的obj物件
		var sing = function(){
		  console.log(that.words);  //這裡的that帶著
		}
		sing(); //'我心裡有針要喊煞嘸聲'
	}
};
obj.sing();  //'我心裡有針要喊煞嘸聲'
```
JavaScript有個有趣的小地方是存到變數that的那行程式碼裡的this，會是指向本身物件的this唷！
所以再來我使用它的時候他會是指向obj這個物件的。
下面的那行console.log也可以顯示出這個this是指哪個物件。

參考文章：[#Javascript：this用法整理](https://software.intel.com/zh-cn/blogs/2013/10/09/javascript-this)
我只是用比較有趣的例子敘述XD。

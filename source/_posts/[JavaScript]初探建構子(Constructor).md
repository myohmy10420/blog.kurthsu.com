---
title: '[JavaScript]初探建構子(Constructor)'
date: 2017-01-19 12:57:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - Constructor
---
學JS這麼久了一直對建構子的觀念很模糊，剛好前陣子尋找技術文章又有提到，自己再來深入了解終於比較理解了，網路上很許多非常好的解釋，但為了防範老人痴呆自己也想來下手寫一篇分享。

MDN的介紹就已經非常不錯，趕時間的直接[參考這篇](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript)吧XD。

在原生的JS世界，我們經常創造很多物件，即使是超級簡單的：
``` javascript
var Person = {
    name: 'Kurt',
    age: 18,
    skill: 'Keep cool'
}
console.log(Person);  //print出Person這個東西骨子裡到底裝了些什麼
```
這樣我定義了一個Person名叫Kurt有年齡和一個可以裝酷的技能，但是我想做二十個人組成一個棒球隊，我就必須宣告二十次這種物件，那有沒有更好的方法呢？

我們請小叮噹幫我們做出一個人類製造機吧！！
``` javascript
function makePerson (name) {
    console.log(name + '出生惹！');
}

var Kurt = new makePerson ('Kurt');  //Kurt出生惹！
var Maggie = new makePerson ('Maggie');  //Maggie出生惹！
console.log(Kurt);  //Object {}
console.log(Maggie);  //Object {}
```
我做了一個人類製造機(function)叫makePerson，這個就是大家所謂的`『類別』`，他可以拿來製造真實的人類(先不要討論Kurt也可以是一隻狗的名字，這樣我會很困擾)。
但是！！這個人類製造機(function)並不是一個實體的人類(物件)唷！！有注意到我在使用這個人類製造機的時候前面加了`new`嗎？這時候的Kurt才是一個真正的人類(物件)，你才可以對他做修改，賦予他彩色或是黑白的人生，因為到目前為止他是一個什麼都沒有的人類(全空的物件)，最後兩行console後print出來的結果是Object {}。

好，身為一個良好的棒球隊要年齡適中，各有各的特長使之各司其職，所以我們要來給我們製造出來的人類(物件)一些特長了(屬性)。

我們針對Kurt延續程式碼：
``` javascript
function makePerson (name) {
    console.log(name + '出生惹！');
}

var Player1 = new makePerson ('Player1');  //Kurt出生惹！
Kurt.name = 'Kurt';
Kurt.age = 18;
Kurt.skill = 'keep cool';
console.log(Player1);  //Object {age: 18, name: "Kurt", skill: "keep cool"}
```
這邊可以看到我先製造Player1的人類(物件)，我後天訓練讓大家要叫他Kurt(name)，給了他現在很適合打棒球的18歲年齡(age)，還有讓他在球場上一直保持冷靜的技能(skill)，剛剛後天訓練的東西我們稱之為`『屬性』`。

可是這邊有個問題，慣老闆不會喜歡我們花這麼多力氣去後天訓練這些球員(每個物件都要一個一個去定義是很麻煩的事情)，我們希望他一加入棒球隊就是個適合打棒球的人，那最棒的事情就是他打從娘胎出來就會打棒球了，沒錯，慣老闆就是這麼任性。

所以我們要改良人類製造機，讓一出廠的人類就有他的名字，年齡和技能，於是乎....：
``` javascript
function makePerson (name, age, skill) {
    this.name = name;
    this.age = age;
    this.skill = skill;
}

var Player1 = new makePerson ('Kurt', 18, 'Keep cool');
console.log(Player1);  //Object {age: 18, name: "Kurt", skill: "keep cool"}
```
新型的人類製造機如上，只要我們製造(new)人類(物件)的時候把後面的參數填正確位置，一出生的人類馬上就可以上場打棒球囉！

好，現在你可以先製造個二十位未成年少女組成一個棒球隊了？

言歸正傳，慣老闆覺得這樣可以直接製作適合打棒球的人還不夠有競爭力，所以他希望從第一棒到第九棒，救援投手到候補球員，甚至是打掃棒球茶水間的大嬸都有擊出全壘打的能力，畢竟還沒做到這一步之前球隊都是有進步空間的，阿只要球隊還有進步空間在慣老闆的眼裡都是一隻還不夠有競爭力的球隊，所以我們要想個『方法』。

再延續之前的程式碼：
``` javascript
function makePerson (name, age, skill) {
    this.name = name;
    this.age = age;
    this.skill = skill;
}

makePerson.prototype.yesBoss = function() {
    console.log('擊出全壘打，得分！');
};

var Player1 = new makePerson ('Kurt', 18, 'Keep cool');
Player1.yesBoss();  //擊出全壘打，得分！
console.log(Player1);
/*Object {
  age: 18,
  name: "Kurt",
  skill: "Keep cool",
  yesBoss: function () {
        console.log('擊出全壘打\uFF0C得分\uFF01');
    }
}*/
```
撰寫新的程式碼，在人類製造機(類別)後面加上.prototype就可以打造製造機專屬的`『方法』`，方法的名稱也是自己定義的，在這邊我取名為yesBoss(是的老闆)。

只要以後由這個人類製造機製造出來的人類使用這個方法就可以打出全壘打了，但一定要是此製造機(類別)製造出來的人類(物件)唷！

接下來只要慣老闆不斷的使用yesBoss就可以讓自己旗下的未成年少女和茶水間大嬸得分到對手不要不要的，每場屌打對手二十幾分，金錢，名聲，地位和權利全部都接收，那這些肯定都是慣老闆的功勞啊！！

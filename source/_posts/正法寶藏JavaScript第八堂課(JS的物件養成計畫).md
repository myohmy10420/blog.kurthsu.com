---
title: '正法寶藏JavaScript第八堂課(JS的物件養成計畫)'
date: 2016-09-19 12:36:00
categories:
 - [生活, 跨行學程式]
---
上次贏了之後這次來比較有多一點信心給老師催殘了，這次難得僅有的五位學生全部到齊了！再來就是腦力激盪的一個下午。

前兩個小時之前老師是先講一半上個禮拜題目的解答，其實都沒有一定的解法，但我想學到更多的是老師的code觀念，達人的live show真的是可遇不可求的東西呀！難怪各行的專業都會有觀眾花錢去看。

在介紹這堂課之前想分享一下超級門外漢學程式的感覺，即使我上次幸運贏得比賽，但只要是新的東西其實真的是不太分難度的XD，總之就是要趕快吞下去在慢慢吸收囉，一開始真的很難細細地去品嚐coding的甜美呀！



##  float problem

一開始老師先稍微提到JavaScript是很古老的語言，所以在程式上會有些小缺陷，像是單純的打出一行console.log(0.1+0.2)其實出來的不會是很剛好的整數0.3而是0.30000000000000004，這就是所謂的 ` float problem ` ，這種問題就要多打一行code去取整數來維持穩定性了。



##  switch最佳選擇器
 ` switch ` 在選擇許多選項中的一個時是非常好用的，我們不用再打上許多if的判斷式，這有點像是給十位人士發號碼牌的概念，叫到哪一個號碼那個人就可以開始行動。

廢話不多說，直接下一個簡單的JavaScript例子：
``` javascript
(function () {                         //養成閉包好習慣，讓程式內的東西由上往下一定執行
   var ATCTION_SLEEP = 0;              //宣告變數，這裡值得一提的是普遍JS淺規則中如果變數全部大寫就是希望
   var ATCTION_WLAK = 1;               //所有維護者都不要去隨意的改變這項變數和它的元素
   var ATCTION_TALK = 2;

   var bot {                           //創造物件
     action: 1
   }

   var human {
     action: 2
   }

   act(bot.action);                    //執行function act且帶入的第一個參數是剛剛物件bot中的action值
   act(human.action);

   function act (action) {             //在這裡action的值被act(bot.action)代入為1
     switch (action) {
       case ATCTION_SLEEP:             //第一個case，在一開始有宣告var ATCTION_SLEEP = 0;故不成立
         console.log("I'm sleep")
         break;                        //記得每個case一定要break停止掉，不然就會一直執行！！
       case ATCTION_WLAK:              //第二個case，在一開始有宣告var ATCTION_WLAK = 1;
         console.log("I'm walking")
         break;
       case ATCTION_TALK
         console.log("I'm talking")
         break;
       default:                        //在上述都沒找到相符項目則執行這行default，不用加break
         console.log("There is nothing I can do")
})();
                                       //再來act(human.action);也會跑一次function act流程，成果
                                       //都可以在檢查程式碼的console裡看到
```



##  try&catch基本除錯好用迴圈之一
當我們做好一個function，卻有很多參數想測試時可以嘗試用這個方法一次搞定！

馬上來進入小小例子：
``` javascript
function doubleIt(num) {                         //創造簡單的function
  if (isNaN(num)) {                             //小技巧！如果帶入的參數不是數字則isNaN會成立所以執行if
    throw num ;
  }
  return num * num;
}

var nums = [2, 3, 'abc'];                        //宣告陣列等一下可以把裡面的參數一次帶入

var res = nums.map(function(num) {               //宣告一個變數把主角try&catch的結果放入裡面，
  try {                                          //nums使用map這個方法把陣列的值一個一個帶進去
    return doubleIt(num);                        //，當try試著執行function doubleIt(num)如
  }                                              //成功則return結果回去，如果失敗則執行catch的
  catch(err) {                                   //東西，把結果歸0後return回去。
    return 0;
  }
});

console.log('res', res);                         //上面都是在電腦中進行，我們必須把結果呼叫給我
                                                 //閱讀。
```


##  .bind()移花接木
 ` .bind() ` 大多數是跟 ` this. ` 一起用的，我們大部分會希望一個function能不斷的重複使用，那他是移花接木什麼呢？看一下下面簡單的範例吧：

``` javascript
function Human() {                             //創造一個Human類別
  this.name = 'human';                         //每次叫出Human這個function則這次的this.name變數
}                                              //都是獨立的，在Human類別裡的東西稱為屬性
Human.prototype.speak = function() {           //創造一個Human類別的方法
  console.log(this.name + ' is speaking');
}
function Bot() {
  this.name = 'bot';
}
Bot.prototype.speak = function() {
  console.log(this.name + ' is s p e aking....');
}
var human = new Human();                       //呼叫類別Human
var bot = new Bot();
human.speak();                                 //human is speaking
human.speak.bind(bot)();                       //bot is speaking，此時類別Human屬性全數使用bot
```


##  .call()移花接木二式
 ` .call() ` 其實跟 ` .bind() ` 有異曲同工之處，簡單的直接看範例吧！

``` javascript
 (function() {

  function Human() {
    this.name = 'human';
  }
  function Bot() {
    this.name = 'bot';
  }

  function showMessage(message) {
    console.log(this.name + ': ' + message);
  }

  var human = new Human();
  var bot = new Bot();

  showMessage.call(human, 'hello ! I am human !');  //human: hello ! I am human !
  showMessage.call(bot, 'hello ! I am bot !');      //bot: hello ! I am bot !
                                                    //我們看function showMessage只有一個參數
                                                    //但這邊使用.call去呼叫Human的this.name
})();
```

這堂課這些方法都是在物件導向中蠻常使用的方法，但是經驗和使用時機就真的要很多的經驗累積囉！

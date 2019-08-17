---
title: '正法寶藏JavaScript第六堂課(最大的撞牆期)'
date: 2016-09-04 12:47:00
categories:
 - [生活, 跨行學程式]
---
隨著上課的東西越來越多，所有的挫折感排山倒海的衝向我的情緒，漸漸開始上課前都會有莫名的緊張感，翻開筆電有點排斥開啟iTerm繼續打code，有時候在想是不是自己的問題，但我想這也許是非本科系的必經之路吧！來分享這兩天的心得吧。

##  正法寶藏Java第六堂課之三次元運算子

第六堂課依然是四個人，不過這次出席的是另一個比較可愛的女同學，但課堂結束後才知道原來他是大魔王XD，一開始介紹的是三元次運算子『？』，以運算子來看其實就是『or』的意思，那以迴圈來看的話他就是一個完整的if和else的組合迴圈。

簡單的語法如下：
` Variable = event ? true : false; `
使用的例子如下：
` leader = vote ? president : king; `
在這裡事件是vote(投票)，問號代表使用三次元運算子，以：左邊是ture(真)右邊為false(假)，搭配使用例子如果vote為真則變數leader帶入true的值president(總統)，反之為假的話則帶入false的值king(國王)。

雖然這是很精簡的寫法可以代替掉使用if和else迴圈的寫法但相對的彈性就相當的小，如果還想多做別的事情或者是去維護它就會相對的很麻煩了，除非很篤定往後不太會在變動了，不然這算是比較show skill的寫法吧！(因為對我來說很困難XD")

##  for & while迴圈

for的遊戲規則為 for (初始執行 ;終止條件 ;每次執行)，下面直接用最基本的範例解釋：

``` javascript
var daysToWork = 22;

for (var i = 0; i < daysToWork; i++) {
  console.log('i = %s', i);
}
```

首先初始執行讓迴圈有個控制變數『i=0』，再來終止條件是『i < 22(剛剛宣告daysToWork = 22)』，反過來也可以說是開始條件i < 22就開始作動，後面『i++』就是每執行一次for迴圈i就會+1，如此一來這個迴圈從i=0直行到i=21總共會執行22次這個迴圈，成功的話可以對顯示地方右鍵檢查原始碼去檢查`console.log`是否動了22次。

那while的遊戲規則為while(執行條件)，其實跟if有點像，一樣帶個小例子：

``` javascript
var i = 0;

while(i < 10) {
 console.log('i = %s', i++);
}
```
在這裡也看到跟for一樣，不過是在外面先宣告好變數i=0，條件i < 10開始執行迴圈，而迴圈使用console.log讓i每次執行完都會+1直到達不到執行條件i < 10。

在迴圈最最最重要的一點是要小心做到無限迴圈，譬如說今天while迴圈裡面console.log沒有打上i++，那麼『i 永遠會 < 10』，這時候通常就得強制關掉作業程式了，如果剛剛都沒保存的話真的是會欲哭無淚啊！

所以在做每個迴圈的時候除了特別小心，最好是先運作簡單的東西看看有沒有問題，不要急著一次做運算量很大的東西，當然最好是迴圈前迴圈後都做好保存的好習慣，不要被自己做出來攻擊力無限的黑暗大法師搞垮了XD。


##  撞牆的無限迴圈

其實這篇文章本來要難產的，因為上完課之後我什麼東西都做不太出來，即使把出的作業拆到一個最小最簡單的功能也一直出錯，真的分享不出什麼心得，直到下午跟Nic在咖啡廳互相討論我其中一個作業，我們一樣把功能拆成好幾個小的，一一做上去再湊起來，畢竟Nic的專精不在JavaScript上面，所以在討論的過程中我很能跟上他的速度和思維，『重點是我也親自做了這些步驟』，不然正法寶藏的老師真的太強了啊！啪拉啪拉突然就把一棟房子蓋好了，而且還是很華麗的那種XD，這邊也分享一下作業心得。

作業：
寫出一個可以出隨機數學題目的表單，並且讓使用者可以作答且顯示答對或答錯。

在這裡我一開始想做的功能就是如何出現數學題目，於是我和Nic研究出使用下列function:
``` javascript
function getRandomArbitrary(min, max) {
  return Math.floor(Math.random() * (max - min)) + min;
}
```
使用的方法就是call getRandomArbitrary()這個function之外在括號裡給他兩個數字，那今天我們需要產生兩個隨機的數字要怎麼辦？

那就是定義兩個變數並且個別執行getRandomArbitrary()一次，於是在全域直接宣告兩個變數
``` javascript
var number1 = getRandomArbitrary(0, 100);
var number2 = getRandomArbitrary(0, 100);
```
這邊Nic也給我一個很重要的觀念，今天用一個function做好了一個功能以後就是放在身邊，需要的時候才再去call他就好，所以其他功能就再做其他function，坐到後面需要哪些function再去把他們叫出來就好，不然一個function要做很多事情會很麻煩而且很容易出錯。

再來我要把題目印出來再宣告如下
``` javascript
var question = document.getElementById("question");
question.innerHTML = number1 + "+" + number2;
```
把值插入id="question"的div的html值裡

題目做出來了回答地方就比較簡單一些，首先在html裡做兩個表格：
``` javascript
<form>
    <input type="text" id="answer">
    <input type="button" value="SUBMIT" onclick="exam()">
</form>
```

先做一個function在背後檢查答案是多少：
``` javascript
function calculate(number1, number2) {
  answer = number1+number2;
  return answer;
}
```

再來再做一個function檢驗提交的值是不是跟背地裡算出的答案相符，程式碼如下：
``` javascript
function exam () {
  var exam = document.getElementsByTagName("input")[0].value;
  answer = calculate(number1, number2)
    if (exam == answer)
      alert("yes");
    else
      alert("No");
  end
}
```

再來登呢～作業就完成啦！

這邊程式的過程算是打給自己看的，不然其他人一定不知道我在幹麻XD，但我想表達的是第六堂課上完兩天後這是我第一次獲得巨大的成就感，很多觀念也會在成就感中吸收得很快，所以在面對越大的挫折感中不如想辦法去把功能拆成一塊塊不斷累積小小的成就感，並且設下停損點，當無法再累積任何成就感的時候就趕快想辦法對外求救吧，不然成就感無法累積的時候挫折感是會成長的，這樣一來一往會很快速的消磨完耐心和熱情，就像我這兩天一樣！

---
title: '[CSS]我看到的浮動元素(float)與clear'
date: 2017-03-31 03:12:00
categories:
 - [CSS]
tags:
 - CSS
 - float
 - clear
---
在工作上遇到了要製作像Line一樣的聊天畫面，起先以為很簡單，但沒想到一路上學了超多東西XD，之前就一直很想知道`clear: both;`是幹嘛的，這次有了更深入的了解。

clear在MDN就可以查到他有很多屬性，這次就分享最常用的`clear: both;`就好(其實過程中我也只用到這個)。

# 浮動元素
什麼是浮動元素？網路上有很多專業解釋，但在這裡我用我觀察和測試的結果用白話解釋。

`浮動元素就是把該元素抓起來漂浮在上面`，聽起來很抽象吧？你可以想像一條橡皮筋綁著一疊紙錢，此時橡皮筋就像一個tag，一個容器(div, p, span...等等)之類的而他的大小被他裡面的紙錢(elements)撐大，而你`紙錢從橡皮筋裡面抽出來的動作就像是給他float的特性`，他在橡皮筋上面了，而橡皮筋因為裡面的紙錢被抽出來而失去了原來的大小。

但這畢竟不是照著物理世界行動的東西，所以他有下列一些特性：
>1.擁有至右或至左的特性(因為float最常用的屬性是left和right而已)。
>2.對容器不具實體(就像上述橡皮筋與紙錢的比喻)。
>3.本身像內聯元素(inline element)一樣排列。
>4.會與同層的內聯元素(inline element)連結。

這是我觀察的四個特性，一個一個解釋很簡單，但一次要面對兩個特性以上的話就會變得很危險，所以建議使用float的時候最好越單純越好，不要一次觸發他太多特性會很難維護，會很想把筆電折成兩半XD。

直接來看範例(或者[點我看線上實作](https://jsbin.com/carabu/2/edit?html,css,output))：
```html html
<div class="wrapper">
    <div class="box">stone</div>
    <div class="floater">ff ff ff ff ff </div>
    <div class="floater">ff ff ff ff ff </div>
    <div class="box">stone2</div>
</div>
```
```css css
.wrapper{
  border: 1px solid blue;
  padding: 10px;
  width: 200px
}
.floater {
  border: 1px solid;
  width: 20px;
  float: left;
}
.box {
  width: 100px;
  border: 1px solid red;
}
```
![螢幕快照 2017-03-31 上午10.39.39.png](http://user-image.logdown.io/user/18810/blog/18323/post/1656549/NgyP9qOBRyumTdZVNKE3_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202017-03-31%20%E4%B8%8A%E5%8D%8810.39.39.png)

首先看充滿ff的黑色框框是我使用`float: left;`讓他產生第一點特性的至右。

再來可以看到他對他的容器有藍色框框的`wrapper`並沒有實質的佔體積，所以黑色框框是直接超出藍色框框的並沒有把它撐大。

第三是他像內聯元素一樣排排站好，就像<span>這種tag一樣，可以看到兩個黑色框框是站在一起的。

最後一點很有趣，`他會跟別人的內聯元素結合！`你可以看到如果第二個紅色框框的stone2還有空間讓兩個黑色框框置入，他會挪出空間，並且右讓黑色框框符合他第一點的特性讓他至左。

再來如果剛剛第二個紅色框框的stone2並沒有空間讓他置入呢？
`他會迫使stone2的內聯元素換行`所以會撐大stone2的紅色框框。
稍微改一下CSS裡的box屬性修改就會有下面效果：
```css css
.box {
  width: 60px;
  border: 1px solid red;
}
```
![螢幕快照 2017-03-31 上午10.38.55.png](http://user-image.logdown.io/user/18810/blog/18323/post/1656549/ml7RNuzS429wlU6x3xK1_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202017-03-31%20%E4%B8%8A%E5%8D%8810.38.55.png)

stone2被撐大了！
那如果我強制設定紅色框框的高度不讓他被撐大呢？
```css css
.box {
  width: 60px;
  height: 40px;
  border: 1px solid red;
}
```
![螢幕快照 2017-03-31 上午10.38.35.png](http://user-image.logdown.io/user/18810/blog/18323/post/1656549/syRFetgRTKQ5qVXHsuqA_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202017-03-31%20%E4%B8%8A%E5%8D%8810.38.35.png)

天阿，雖然紅色框框的區塊元素(block element)排列特性是正確的，但裡面的內聯元素卻被搞到離家出走了！！

如果還有人可以這樣子維護和開發的下去我真的會很欽佩他，如果是我到這裡就會想要砍掉重練了XD，所以說在使用浮動元素的時候最好是用單純的使用越好，不要一次使用它太多特性。

# clear
首先可以直接去觀察[MDN提供的幾個範例](https://developer.mozilla.org/en-US/docs/Web/CSS/clear)玩玩看。

我觀察出的特性：
>clear可以把一個元素左右邊的浮動元素清除掉，所謂的清除掉並不是刪除他，而是一個王不見王的感覺，不是你換行就是我要換行，反正不是在同一行就對了！

clear使用的情況很多，這邊只分享我遇到的問題，我想要做一個類似像賴聊天的泡泡框，當然做左邊的沒有問題，最大的問題是右邊的泡泡框。

所以我需要把裡面的一個區塊至右，並且因為區塊內的元素多寡大小而自動向左邊變寬或變長，最後外面的容器也要隨著裡面的區塊改變高度，所以統計一下我要做的事情：

1.容器(wrapper)隨著區塊的大小改變高度，這裡用`padding: 10px;`可以做到。
2.區塊可以隨著裡面元素的多寡大小自動變更寬或長，這裡用`display: inline-block;`可以做到。
3.我需要把區塊至右，而且區塊是向左邊變寬，這裡用`float: right;`可以做到。

那我們來開心寫code吧！
```html html
<div class="wrapper">
  <div class="block">
   text<br>
   I real want succeed
   </div>
</div>
```
```css css
.wrapper{
  width: 200px;
  border:1px solid black;
  padding:10px;
}
.block {
  border: 1px solid blue;
  display: inline-block;
  float: right;
}
```
![螢幕快照 2017-03-31 上午11.10.17.png](http://user-image.logdown.io/user/18810/blog/18323/post/1656549/Q2YE5UFkTy6dpPmimypM_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202017-03-31%20%E4%B8%8A%E5%8D%8811.10.17.png)

發生什麼事情了！？？？
為什麼我的wrapper包不住它！？
我想想阿靠就只是因為float的第一個特性讓他撐不開容器而已，換個屬性也可以囉！於是...

display: absolute;會產生跟float一樣的漂浮特性。
失敗。

把泡泡框margin和padding到最右邊後但是泡泡框是往右邊長。
失敗。

做一個區塊元素去比照這個泡泡框的高度一起長高？我好像找不到這個方法...XD。
失敗中的失敗。

想來想去查來查去剛好看到了有人說clear這個方法可以撐開因為float而撐不開的容器！
只要在容器最後面加上一個擁有`clear: both;`的tag就行了！

於是乎...：
```html html
<div class="wrapper">
  <div class="block">
   text<br>
   I real want succeed
   </div>
   <div class="clear"></div>
</div>
```
```css css
.wrapper{
  width: 200px;
  border:1px solid black;
  padding:10px;
}
.block {
  border: 1px solid blue;
  display: inline-block;
  float: right;
}
.clear {
  clear: both;
}
```
![螢幕快照 2017-03-31 上午11.10.06.png](http://user-image.logdown.io/user/18810/blog/18323/post/1656549/cX77B1xSYWIOAikc86oW_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202017-03-31%20%E4%B8%8A%E5%8D%8811.10.06.png)

終於做出我要的效果了...這是眼淚嗎？？
終於可以告老返鄉了～耶～～！！

最後也推薦看看這個部落客寫的[详解CSS float](http://luopq.com/2015/11/08/CSS-float/)属性哦！

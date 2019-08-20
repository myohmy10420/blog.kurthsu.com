---
title: '[CSS]用transition, before, after 客製會滑動的按鈕 buttom'
date: 2017-07-21 10:06:00
categories:
 - [CSS]
tags:
 - CSS
 - transition
 - before
 - after
 - buttom
---

最近要試著做有動畫的按鈕，當hover的時候會有一個icon刷新的感覺，可以看[Demo](https://codepen.io/KurtHsu/pen/KqdQzN)。

## 先認識:before :after
每個class都有:before :after這兩個偽元素，你可以想像成創造了一個class="btn"後會變成：
``` css
.btn {}
.btn:before {}
.btn:after{}
```
不管css寫在哪都會有效果，值得注意的是`:before和:after一定要有content這個屬性才有效果`，而`content是內文的意思，像是innerHtml`。
下列是同等效果：
正常元素
``` html
<style>
    .btn { color: red; };
</style>
<div class="btn">Btn</div>
```
偽元素
``` html
<style>
    .btn:before {
        content: 'Btn';
        color: red;
    };
</style>
<div class="btn"></div>
```
接下來進入主題

## 先做出有icon的原生還沒有動畫的buttom
``` html
<div class="btn">
  <p class="icon"></p>
  <span>Search</span>
</div>
```
``` css
.btn {//btn的容器
  text-align: center;
  width: 100px;
  height: 30px;
  margin: 1em;
  padding: 0px 10px 0px;
  border: 1px solid;
  border-radius: 20px;
  cursor: pointer;
}

.btn .icon {//會滑動的icon
  margin: 0px;
  padding: 0px;
  display: inline-block;
  position: relative;
  right: 14px;
  height: 29px;
  width: 29px;
  border: 1px solid;
  border-radius: 999px;
  background-color: #fff;
  z-index: 999;
}

.btn span {//一開始的內文
  opacity: 1;
  position: relative;
  bottom: 10px;
  right: 10px;
}

.btn:after {//icon滑動後更新的內文
  content: 'Go now';
  opacity: 0;//更新前是看不到的所以把他能見度設定0
  display: inline-block;
  position: relative;
  bottom: 32px;
  right: 10px;
}
```
接下來要注意的是`transition(動畫)設定在初始的class裡面`，所以我們更新後把CSS更新如下(記得我們還沒創造動畫唷！)：
``` css
.btn {
  text-align: center;
  width: 100px;
  height: 30px;
  margin: 1em;
  padding: 0px 10px 0px;
  border: 1px solid;
  border-radius: 20px;
  cursor: pointer;
}

.btn .icon {//會動所以加上動畫屬性
  margin: 0px;
  padding: 0px;
  display: inline-block;
  position: relative;
  right: 14px;
  height: 29px;
  width: 29px;
  border: 1px solid;
  border-radius: 999px;
  background-color: #fff;
  z-index: 999;
  -webkit-transition: 0.6s;
  -moz-transition: 0.6s;
  -o-transition: 0.6s;
  transition: 0.6s;
}

.btn span {//會動(要消失)所以加上動畫屬性
  opacity: 1;
  position: relative;
  bottom: 10px;
  right: 10px;
  -webkit-transition: 0.6s;
  -moz-transition: 0.6s;
  -o-transition: 0.6s;
  transition: 0.6s;
}

.btn:after {//會動(要出現)所以加上動畫屬性
  content: 'Go now';
  opacity: 0;
  display: inline-block;
  position: relative;
  bottom: 32px;
  right: 10px;
  -webkit-transition: 0.6s;
  -moz-transition: 0.6s;
  -o-transition: 0.6s;
  transition: 0.6s;
}
```
最後我們`觸發動畫是利用hover`，所以把動畫的結果寫出來，css更新如下：
``` css
.btn {
  text-align: center;
  width: 100px;
  height: 30px;
  margin: 1em;
  padding: 0px 10px 0px;
  border: 1px solid;
  border-radius: 20px;
  cursor: pointer;
}

.btn .icon {
  margin: 0px;
  padding: 0px;
  display: inline-block;
  position: relative;
  right: 14px;
  height: 29px;
  width: 29px;
  border: 1px solid;
  border-radius: 999px;
  background-color: #fff;
  z-index: 999;
  -webkit-transition: 0.6s;
  -moz-transition: 0.6s;
  -o-transition: 0.6s;
  transition: 0.6s;
}

.btn:hover .icon {//讓icon向右邊跑
  right: -77px;
}

.btn span {
  opacity: 1;
  position: relative;
  bottom: 10px;
  right: 10px;
  -webkit-transition: 0.6s;
  -moz-transition: 0.6s;
  -o-transition: 0.6s;
  transition: 0.6s;
}

.btn:hover span {//讓初始內文消失
  opacity: 0;
}

.btn:after {
  content: 'Go now';
  opacity: 0;
  display: inline-block;
  position: relative;
  bottom: 32px;
  right: 10px;
  -webkit-transition: 0.6s;
  -moz-transition: 0.6s;
  -o-transition: 0.6s;
  transition: 0.6s;
}

.btn:hover::after {//讓更新內文出現
  opacity: 1;
}
```

這樣就大功告成囉！！！！

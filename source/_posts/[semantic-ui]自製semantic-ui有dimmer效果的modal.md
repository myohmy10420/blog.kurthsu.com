---
title: '[semantic-ui]自製semantic-ui有dimmer效果的modal'
date: 2017-08-10 04:48:00
categories:
 - [semantic-ui]
tags:
 - semantic-ui
 - dimmer
 - modal
---
## 先了解原生ui範例和我們要做什麼
我們先來看看在semantic-ui的modal有什麼效果，[請點連結](https://semantic-ui.com/modules/modal.html)裡按他的Run Code就有範例。

其實後面暗掉的布幕像是他們dimmer的效果，[請點連結](https://semantic-ui.com/modules/dimmer.html)裡按他的Run Code就有範例。

所以我們要做的事情如下：
`1. 有一個dimmer布幕。`
`2. 有一個浮在前面的modal(div等等)。`

## 簡單實作
html 先做好簡單的三個東西：
1. 當背景布幕的dimmer
2. 啟動modal的button
3. 要顯示的modal
```html
    <div class="dimmer"></div>
    <button onclick="openModal()">open modal</button>
    <div class="modal">I'm modal<div>
```
給他們css，先假裝page高度1000px，所以我直接先設body的height: 1000px;，接下來有兩件事情：
1. 先把dimmer和modal用dislay: none;隱藏起來
2. modal要在dimmer之前，並且dimmer要覆蓋著整個page，所以我們利用z-index做圖層分割
``` css
body {
  height: 1000px;
}
.dimmer {
    display: none;
    position: relative;
    background-color: rgba(0, 0, 0, 0.53);
    width: 100%;
    height: 100%;
    position: fixed;
    z-index: 2;
}
.modal {
    display: none;
    position: relative;
    background-color: #fff;
    text-align: center;
    padding-top: 10px;
    border: 1px solid #fff;
    border-radius: 8px;
    width: 100px;
    height: 30px;
    margin: 0 auto;
    z-index: 3;
}
```
接著重頭戲JS利用delegate這個神奇的原生函示做出顯現modal和點擊dimmer就可以關掉modal的畫面：
``` javascript
function openModal () {
  $('.dimmer').fadeIn(function () {
    $('.modal').fadeIn();
  });
}
$(document).ready(function() {
  $(document).delegate('body', 'click', function(event) {
    $('.modal').fadeOut(function () {
      $('.dimmer').fadeOut();
    });
  });
  $(document).delegate('button', 'click', function(event) {
    event.stopPropagation();
  });
});
```
初步認識一下`delegate的觸發時間點是mousedown`，如果想搞其他的變化了解這點很重要！！
還有`z-index一定要搭配position: relative;或者是absolute;不可以使用預設的static`！
這兩點注意好應該就可以實作出來想要的效果了，可以參考我直接在[codepen上做的範例](https://codepen.io/KurtHsu/pen/BdRezJ?editors=1111)。

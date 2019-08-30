---
title: '[Rails]非常方便的搭配window.open開啟遊覽器新視窗'
date: 2019-08-31 00:30:38
categories:
 - [Rails]
 - [JavaScript]
tags:
 - Rails
 - JavaScript
 - window.open
---
通常好的使用者體驗有許多前端套件製作pop的效果, 例如[boostrap的modal](https://getbootstrap.com/docs/4.0/components/modal/), 如果不是很在意這些體驗, 例如在後台只有內部人員使用的情況下其實window.open就很夠用, 而且十分地搭配Rails

## 製作js
window.open的參數為`window.open(url, window name, config)`, 以這個為基礎做以下js
```
$(document).ready(() => {
  $("[data-popup-window]").click(() => {
    let windowName = this.getAttribute("data-popup-window") || null;
    let url = this.getAttribute("href") || this.getAttribute("data-url");
    let config = this.getAttribute("data-popup-config") || "";
    window.open(url, windowName, config);
    return false;
  })
})
```
1. 利用data-attributes來註冊事件更棒, 可以更避免註冊污染且更有辨識度
2. url可接受a tag本身的herf, 或者想自己刻一個button也行, 只要給data-attributes即可

## 製作按鈕
以下範例用a tag介紹, 總之在html要看到該tag有`data-popup-window`這個data-attributes即會觸發剛剛註冊的事件, 如果是想自己刻一個button的話記得還要給`data-popup-url`
```
<%= link_to("開啟新視窗", some_path, data: {
  popup_window: "windowOpen",
  popup_config: "height=600,width=1000"
}) %>
```
寫完看一下render出來的html是否都正確, 雖然這邊是寫popup_window, 但在html上應該會顯示data-popup-window, 就可以用剛剛寫的$("[data-popup-window]")註冊到了

## controller
再來製作好some_path的router, controller裡的action最後加上`render layout: false`, 就可以自行寫全新的html, 當然想要載入到自己的layout也行, 只是這樣的需求似乎就不太需要window.open
```
def some_action
  ＠data = 'Hello world!'
  render layout: false
end
```

## view
最後寫這個action對應的view, 因為剛剛render layout: false所以得寫個全新完整的html, 這邊顯示一個簡單的Hello world
```
<!DOCTYPE html>
<html>
  <meta charset="UTF-8">
  <%= stylesheet_link_tag "your_css" %>
</head>
<body>
  <%= @data %>
  <%= javascript_include_tag "your_javascript" %>
</body>
</html>
```
大功告成, 一樣可以拿到controller裡的所有資料, 十分的方便！

window.open還有一些config可以參考[JavaScript window.open() 開新視窗以及屬性設定](https://www.wibibi.com/info.php?tid=95)

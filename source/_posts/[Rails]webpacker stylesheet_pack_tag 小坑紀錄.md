---
title: '[Rails]webpacker stylesheet_pack_tag 小坑紀錄'
date: 2020-04-22 23:06:58
categories:
 - [Rails, Webpacker]
tags:
 - Rails
 - Webpacker
---

這篇是記錄結果, 沒想到這個小坑花了我三四個小時才弄出來
難以追蹤的是我一直沒有注意到本來監聽 application.js 的動作已經被同名的檔案蓋過去

先說官方檔案架構:
```
app/javascript:
  ├── packs:
  │   # only webpack entry files here
  │   └── application.js
  └── src:
  │   └── application.css
  └── images:
      └── logo.svg
```

再來就是有一段官方敘述
```
You can then link the JavaScript pack in Rails views using the javascript_pack_tag helper. If you have styles imported in your pack file, you can link them by using stylesheet_pack_tag
```

其他沒有提及太多東西

## 跌坑開始

我本來想做的事情是在 rails 專案裡面一切的 js 和 css 都交由 webpacker 管理, 所以我的檔案結構如下:
```
app/javascript:
  ├── packs:
  │   └── application.js
  │   └── application.scss
  └── styles:
      └── some.scss
```

然後 application.css 去 import 所有 css, 最後在 html 下兩個 tag:
``` html
<%= stylesheet_pack_tag 'application' %>
<%= javascript_pack_tag 'application' %>
```

神奇的事情發生了, 我不論怎麼改 application.js, webpack-dev-server 都不會有反應了, 搞了很久才發現在資料夾 app/javascript/packs 底下原來檔案主名稱不能相同..., 本來 webpack-dev-server 監聽 application.js 的行為被相同檔案主名稱的 application.scss 蓋過去了...

#### development 解決方法很簡單

js 檔案去 require css 檔案就好不用多此一舉創造什麼 application.scss, 然後也會發現 html 不加 <%= stylesheet_pack_tag 'application' %> css 依然運行的很棒

那為什麼要出一個 stylesheet_pack_tag 然後也沒講得很清楚是幹嘛用的給我誤導RRR

## extract_css

原來在 webpacker.yml 有一個 extract_css 設定 default 在 development 環境是 false, 然後在 production 環境是 true

extract_css 顧名思義是把 css 的東西提出來, 如果設定 true 的話會發現剛剛 require 的 css 沒用惹!

## 目前解決方法

extract_css 不管有沒有設定為 true, html 都把兩個 tag 帶上

``` html
<%= stylesheet_pack_tag 'application' %>
<%= javascript_pack_tag 'application' %>
```

檔案結構改為如下:
```
app/javascript:
  ├── packs:
  │   └── application.js
  └── styles:
      └── application.scss
      └── some.scss
```

js 依然 require application.scss
```javascript application.js
require("../styles/application.scss")
```

application.scss 當作主要 css 的載入檔案
```scss application.scss
@import 'bulma';

@import './some.scss';
```

這樣既不會衝檔名, 也不會因為在 produciton 環境時 extract_css 為 true 而找不到 css 了

## 心得

不過最後的疑問是 extract_css false 有啥好處呢? 既然跟 production 行為有落差為何不同步就好QQ

然後 stylesheet_pack_tag 真的蠻少資訊的, 也不確定我理解的是否正確

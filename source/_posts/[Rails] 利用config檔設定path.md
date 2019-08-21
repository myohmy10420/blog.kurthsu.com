---
title: '[Rails]利用config檔設定path'
date: 2018-08-22 03:28:00
categories:
 - [Rails]
tags:
 - Rails
---
最近專案在實施前後端分離，所以把有關於前台的一切刪除，但會造成前端路徑會報錯，例如posts_path就爆炸了，所以我們要有替代方案，但這個替代方案前提是local和production的資料同步，至少local端的資料production上都有。

#### 能用的狀況：
>1. 有production機台
>2. local的資料庫資料production必須都有

#### 原理：
>創造的路徑是直接連到production作為前後端分離實施中的替代方案

## 設定config
我在config資料夾下建立yml檔，檔名任意
```yaml asswt_host.yml
development: &DEV
  host_domain: "http://domian.com"
```
設定了在開發環境下的變數
再來給環境config檔吃
```ruby development.rb
…
  config.x.asset_hosts = config_for('asset_host').symbolize_keys
end
```

### 第一種方法利用routes.rb
```ruby routes.rb
resources :posts, constraints: { host: Rails.application.config.x.asset_hosts[:host_domain], port: "" }
```
此時view即可正常使用post_url
記得port改為空字串不然domain會被加上port

### 第二種方法利用helper
```ruby posts_helper.rb
def post_preview_url(post)
  host = Rails.application.config.x.asset_hosts[:host_domain]
  path = Rails.application.routes.url_helpers.post_path(Post.last)
  URI.join(host, path).to_s
end
```
此時view即可正常使用post_preview_url(Post)

### 小結
看到這裡可能會想說嗯？那我設完routes就好何必多此一舉設定helper？

我這裡的應用情況是應急前後端分時關於前端路徑全爆的問題，所以routes裡的許多路徑是已存在的狀況要去修改

方法一是利用constraints直接去改舊的routes路徑
方法二則是利用舊的路徑生成helper使用
當然還是會有其他的做法就看怎麼規劃比較適合自己的專案

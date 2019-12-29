---
title: '[Rails]Rails 5.1+ import react by webpacker'
date: 2019-12-29 16:36:02
categories:
 - [Rails, Webpacker]
 - [Rails, React]
tags:
 - Rails
 - Webpacker
 - React
---
Rails 5.1+ 新專案已經可以在新建Rails專案的時候順便安裝webpakcer
`$ rails new myapp --webpack`
但如果是舊有專案的話就得自己安裝,

## 參考環境
rails 5.2.3
ruby 2.4.4
node v12.14.0
yarn 1.10.1
假設已經裝好

## install webpack
參考[官方網站](https://github.com/rails/webpacker)的步驟
``` ruby Gemfile
  gem 'webpacker', '~> 4.x'
```
`$ bundle install`
`$ bundle exec rails webpacker:install`

其中 `babel.config.js` 顧名思義就是設定babel的東西, 先用預設的即可
再來 `bin/webpack` `bin/webpack-dev-server` `config/webpack/` `config/webpacker.yml` 都是webpack的設定檔, 先注意 `config/webpacker.yml`這隻檔案最上面的四個參數

```yml webpakcer.yml
  source_path: app/javascript
  source_entry_path: packs
  public_root_path: public
  public_output_path: packs
```

* source_path:
> webpakcer在rails server開啟的時候也會開啟, 預設webpakcer會去偵測`source_path`底下的所有檔案有沒有更新過, 如果有的話只要一刷新頁面server log就會有Compiling...的訊息, 就是webpakcer在作動了

* source_entry_path:
> 會去packs這個資料夾底下找尋`manifest.json`這個檔案, 然後去讀取指定的js or css file, 預設自帶版本控制

* public_root_path:
> webpacker檔案的根目錄, 預設是public, 所以剛剛的source_entry_path真正的路徑會是`public/packs`

* public_output_path:
> webpacker壓縮檔案完要放到哪, 真正的路徑會是`public/packs`, 可以在`public/packs/js`底下找到壓縮出來的js檔案

## 引入webpacker file
webpacker檔案引入方法跟rails原本的javascript_include_tag與stylesheet_link_tag一樣, 不同的是js引入要放在body最底下, helper是`javascript_pack_tag` `stylesheet_pack_tag`
```html
<!DOCTYPE html>
<html>
  <head>
    <title>RailsWebpackerReact</title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
    <%= stylesheet_pack_tag 'application' %>
  </head>

  <body>
    <%= yield %>
    <%= javascript_pack_tag 'application' %>
  </body>
</html>
```
在開發者模式應該就會看到預設的console.log

最後官方網站補充說在一開始的js印入
```js
import "core-js/stable";
import "regenerator-runtime/runtime";
```
core是加強js, regenerator是可以讓開發者不論寫ES6 or ES5最後都會輸出一致, 直接收尋名稱都可以找到該官方Github

## import react
webpacker已經包裝的很好, 只要一鍵指令
`$ bundle exec rails webpacker:install:react`
會發現babel什麼的都已經幫我們更新好, 甚至還給了一個最基本的檔案hello_react.jsx, 會在畫面最下面印出一個Hello React!

最後我們只要在webpacker(app/javasript/packs)資料夾底下的application.js加入一行
```js
  import "./hello_react";
```
重整頁面後就可以在Rails專案開發React了

參考文章:
[Webpacker官方網站](https://github.com/rails/webpacker)

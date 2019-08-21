---
title: '[Rails]實作套入Bootstrap&認識Asset Pipeline'
date: 2018-04-09 04:55:00
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - Bootstrap
 - Asset Pipeline
---
## 實作套入Bootstrap
---
1.先在Gemfile安裝套件
``` javascript Gemfile
gem 'bootstrap-sass'
```
`$ bundle install`

2.把總管理css的檔案改成scss即可以用scss的語法

`$ mv app/assets/stylesheets/application.css app/assets/stylesheets/application.scss`

3.css載入這兩個官方文件敘述所需

``` scss app/assets/stylesheets/application.scss
@import "bootstrap-sprockets";
@import "bootstrap";
```

> 現在我們成功套入Bootstrap而且所有的css檔案都可以用@import的手法載入了

4.js載入這個官方文件敘述所需

``` javascript app/assets/javascripts/application.scss
//= require bootstrap-sass/assets/javascripts/bootstrap-sprockets
```

- [bootstrap-sass官方文件](https://github.com/twbs/bootstrap-sass#a-ruby-on-rails)



## 認識Asset Pipeline
---
現在隨著專案越大以及網站讀取的效要求，我們希望一個html就讀取一隻js檔案和css檔案就好，但我們也不可能把所有程式碼都擠到這兩隻檔案裡面去維護兩個有成千上萬的程式碼。

此時不只Rails有這種壓縮統整功能，在單純前端的世界webpak之類的套件都在做這種事情，而原本的Rails專案裡面的app/assets/javascripts/application.js和app/assets/stylesheets/application.css就是在做這種事情。

``` javascript app/assets/javascripts/application.js
// This is a manifest file that'll be compiled into application.js, which will include all the files
// listed below.
//
// Any JavaScript/Coffee file within this directory, lib/assets/javascripts, vendor/assets/javascripts,
// or any plugin's vendor/assets/javascripts directory can be referenced here using a relative path.
//
// It's not advisable to add code directly here, but if you do, it'll appear at the bottom of the
// compiled file. JavaScript code in this file should be added after the last require_* statement.
//
// Read Sprockets README (https://github.com/rails/sprockets#sprockets-directives) for details
// about supported directives.
//
//= require jquery
//= require jquery_ujs
//= require turbolinks
//= require_tree .
//= require bootstrap-sass/assets/javascripts/bootstrap-sprockets
```
載入的方法為:
> //= require_tree someJSpath

``` scss app/assets/stylesheets/application.scss
/*
 * This is a manifest file that'll be compiled into application.css, which will include all the files
 * listed below.
 *
 * Any CSS and SCSS file within this directory, lib/assets/stylesheets, vendor/assets/stylesheets,
 * or any plugin's vendor/assets/stylesheets directory can be referenced here using a relative path.
 *
 * You're free to add application-wide styles to this file and they'll appear at the bottom of the
 * compiled file so the styles you add here take precedence over styles defined in any other CSS/SCSS
 * files in this directory. Styles in this file should be added after the last require_* statement.
 * It is generally better to create a new file per style scope.
 *
 *= require_tree .
 *= require_self
 */

@import "bootstrap-sprockets";
@import "bootstrap";
```

載入的方法為與剛剛的application.js雷同
`如果有操作套入boostrap的動作現在application是scss不是css`就可以用下面這個方式import進來:
> @import "scssFilepath"

- [Ruby on Rails 實戰聖經](https://ihower.tw/rails/assets-pipeline.html)

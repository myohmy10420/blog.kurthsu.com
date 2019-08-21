---
title: '[Rails]scss and javascript in specific page'
date: 2018-04-23 11:50:00
categories:
 - [Rails]
tags:
 - Rails
 - scss
 - javascript
---
參考文章：
[Rails實戰聖經](https://ihower.tw/rails/assets-pipeline.html)


## 指定引入CSS的方法
---

在`application.css的default`值都會因為有`require_tree .`把底下所有的檔案透過`Asset Pipeline
`全部壓縮成一個css檔案。

但我們想要做得到是：
1.不透過`require_tree .`引入全部檔案只想引入我們想要的
2.在特定的頁面只執行想要的css

第一點把require_tree拿掉自己一個個require或import近來當然沒問題。
第二點是`引入的東西都是全局的`，意思是說當rails g controller的時候專案雖然會自動幫你產生出css檔案，`但這些檔案是互通的`，因為他們終究會被壓縮成單一隻js和css。

方法有很多但這篇記錄兩個，並且用SCSS做示範：

#### 第一個方法給予每個template獨特的id

我們可以在view的最外層包一個id：
``` html home.html.erb
<div id="home">
  <h1 class="title">Hello world</h1>
</div>
```

然後在他自己的scss裡面寫上：
``` css home.scss
#home {
	h1 {
		color: red;
	}
}
```

這樣的優點依然可以利用專案製造出來的scss很快速的找到該頁面的css，但要怎麼想id名稱就是一道題目了。

且有人會說那application.scss還不是都要import一堆檔案為什麼要捨棄require_tree這個方便的東西。
我認為當scss檔案一多規劃變得很複雜之外最實用的一個狀況是在全面更新或停用一隻scss檔案的時候舊的檔案可以依然保留在app/assets/stylesheets/這個目錄下不用被移除，不然如何處理舊的scss也是一道難題。


#### 第二個方法利用 stylesheet_link_tag

1.`一定要移除require_tree`
2.`要去修改config/initializers/assets.rb這隻檔案`

全域的東西依然先引進app/stylessheets/application.scss這隻檔案，此時如果沒把require_tree拿掉他又會把它吃進去了！

假設有一隻檔案要單獨在一個頁面引入，例如style.scss，則進行以下修改：

``` ruby config/initializers/assets.rb
# Be sure to restart your server when you modify this file.

# Version of your assets, change this if you want to expire all your assets.
Rails.application.config.assets.version = '1.0'

# Add additional assets to the asset load path
# Rails.application.config.assets.paths << Emoji.images_path

# Precompile additional assets.
# application.js, application.css, and all non-JS/CSS in app/assets folder are already added.
Rails.application.config.assets.precompile += %w( style.scss )
```

`重啟server`

然後到我們的view裡面：
``` html view.html.erb
<div id="view">
	<h1 class="title">Hello world</h1>
</div>
<%= stylesheet_link_tag "style" %>
```
就可以了！

目前記錄下這兩種方法，管理css方式一定有更多手法也必須對應專案的需求，就看看到時候遇到的情況如何了。


## 指定引入Javascript的方法
---

原因跟css一樣是想擺脫`require_tree .`把底下所有的檔案透過`Asset Pipeline`全部壓縮成一個js檔案。

#### 方法一如同css的方法二利用 javascript_include_tag

假設有一隻檔案要單獨在一個頁面引入，例如welcome.js，則進行以下修改：

``` ruby config/initializers/assets.rb
# Be sure to restart your server when you modify this file.

# Version of your assets, change this if you want to expire all your assets.
Rails.application.config.assets.version = '1.0'

# Add additional assets to the asset load path
# Rails.application.config.assets.paths << Emoji.images_path

# Precompile additional assets.
# application.js, application.css, and all non-JS/CSS in app/assets folder are already added.
Rails.application.config.assets.precompile += %w( welcome.js )
```

`重啟server`

然後到我們的view裡面：
``` html view.html.erb
<div id="view">
	<h1 class="title">Hello world</h1>
</div>
<%= javascript_include_tag "welcome" %>
```
就可以了！

此時與引入在application.js最大的不同是，`每次重進這個頁面這行js就會在執行一次`，也就是每次進這個頁面這個檔案的js就會執行一次！

## 第二個方法是利用TURBOLINKS

這邊先記錄下來這三篇可以參考的文章：
[Rails Guides](https://rails.ruby.tw/working_with_javascript_in_rails.html#turbolinks-%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86)

[ORGANIZING JAVASCRIPT IN RAILS APPLICATION WITH TURBOLINKS](https://brandonhilkert.com/blog/organizing-javascript-in-rails-application-with-turbolinks/)

[Page Specific Javascript in Ruby on Rails](https://www.driftingruby.com/episodes/page-specific-javascript-in-ruby-on-rails)

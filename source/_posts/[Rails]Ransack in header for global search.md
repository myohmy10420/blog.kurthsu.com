---
title: '[Rails]Ransack in header for global search'
date: 2018-05-10 07:20:00
categories:
 - [Rails, Gem]
 - [Ransack]
tags:
 - Rails
 - Gem
 - Ransack
---
基本的使用可以先參考這些資料：
[ransack官方文件](https://github.com/activerecord-hackery/ransack)
{% post_link '[Rails]ransack最基本使用' '[Rails]ransack最基本使用' %}

如果照著{% post_link '[Rails]ransack最基本使用' '[Rails]ransack最基本使用' %}做出來之後把它放在header跳到其他頁面會報錯，因為他會找不到@q這個變數，但要在每個controller加上@q又不切實際，最好的辦法就是把它獨立出來吧！

雖然他是一個form表單，但還是用get比較好，post固然也會運作，但如果原地重整頁面的話就會爆炸嘍！

所以我們先做一個router給他
``` ruby routes.rb
get "search", :to => "search#index"
```

做一個controller
`$ rails g controller search`

設定如下：
``` ruby search_controller.rb
class SearchController < ApplicationController
  def index
    @q = Model.ransack(params[:q])
    @models = @q.result(distinct: true)
  end
end
```

再來view就是action到這個router就好：
``` ruby search.html.erb
<%= search_form_for @q, url: search_path do |f| %>
  <%= f.search_field :title_cont %>
  <%= f.submit "搜尋" %>
<% end %>
```

這樣其實是創立一個獨立的頁面，也就是說如果本來就有model#index的頁面其實search#index的頁面會是獨立且一模一樣的喲！

但此時我們發現只要她去別的頁面會因為別的controller裡面沒有@q這個變數爆error，我們來設訂一個全域變數吧：
``` ruby search_controller.rb
class ApplicationController < ActionController::Base
  before_action :set_ransack_argument

  def set_ransack_argument
    @q = Model.ransack(params[:q])
  end
end
```
這樣的效果就可以讓整個網站都可以使用了，那我們也可以把剛剛search_controller.rb的`@q = Model.ransack(params[:q])`這行拿掉！

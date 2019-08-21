---
title: '[Rails]淺談Strong parameters機制'
date: 2018-04-10 14:46:00
categories:
 - [Rails]
tags:
 - Rails
---
## 起源為防止massive-assignment問題
在Rails有個便利的機制是將表單送出之後透過massive-assignment 的技巧直接 mapping 進 Model 裡

例如在一個簡單的表單
``` ruby index.html.erb
<%= form_for @post do |f| %>
  <%= f.text_field :title %>
  <%= f.text_area :content %>
  <%= f.sumbit "Submit" %>
<% end %>
```

在controller裡的create
``` javascript post_controller.js
class PostController < ApplicationController
  def create
     @post = current_user.posts.build(params[:post])
     if @post.save
       # do something
     else
       # do another thing
     end
  end
end
```

就可以直接成立，但如果有心人士在DOM裡面加了額外參數並且猜對了就可以做到超出權限以外的事情，最直接的就是猜中了user 權限是用 user.is_admin 作為 boolean 值，然後直接讓自己有了admin的權限。



## Rails 4 有了Strong Parameters的機制
在Rails 3 裡面其實也可以手動做簡單的防護機制，但大多人嫌麻煩或者較沒經驗的開發者沒有這個觀念，這個防護機制在許多中小型網站是被忽略掉的，自從Github被Hack了之後Rails團隊把這個機制改為必定要做了！

現在我們的params都要遵守Strong Parameters機制寫成如下
``` javascript post_controller.js
def create
  @event = Event.new(event_params)
  @event.save

  redirect_to :action => :index
end

private

def event_params
  params.require(:event).permit(:name, :description)
end
```

其中：
1. require會把:post這個model相關的參數給抓出來，如果根本沒有回傳:post相關參數，就會產生錯誤訊息。
2. permit就是設定有哪些參數可以傳入，例如上面沒有寫參數，就算你從http request當中傳入，也會回傳nil當做沒東西。

從今以後外人想利用修改DOM把post出去的東西亂加料就會被過濾掉了！

參考文章：
[從 Github 被 hack，談 Rails 的安全性（ mass-assignment ）](http://blog.xdite.net/posts/2012/03/05/github-hacked-rails-security/)
[Rails實戰聖經](https://ihower.tw/rails/basic.html)
[Rails 當中的 params 是什麼？](https://ithelp.ithome.com.tw/articles/10161397)

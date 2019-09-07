---
title: '[Rails]基本Routes設計'
date: 2018-07-20 15:23:00
categories:
 - [Rails, Routes]
tags:
 - Rails
 - Routes
---
在之前文章{% post_link '[Rails]簡單的掌握Routes' '[Rails]簡單的掌握Routes' %}, Rails在基本的resources使用上已經非常方便, 但在工作上第一次遇到shallow等用法的時候即使rake routes出來仍然一頭霧水為什麼會出現這樣的結果, 所以記錄一下自己研究後的觀念

## 此篇目的
1. 理解Routes邏輯
2. 嵌套資源(Nested resources)
3. 認識shallow
4. 認識namespace
5. 漂亮的客製化例如 admin/posts/generator 這種route

## 理解Routes邏輯
先以最基本的resources :posts當範例
```
Rails.application.routes.draw do
  resources :posts
end
```
`$ rake routes`得到的結果

 Prefix    | Verb   | URI Pattern               | Controller#Action
-----------|:------:|--------------------------:| ------------------
 posts     | GET    | /posts(.:format)          | posts#index
           | POST   | /posts(.:format)          | posts#create
 new_post  | GET    | /posts/new(.:format)      | posts#new
 edit_post | GET    | /posts/:id/edit(.:format) | posts#edit
 post      | GET    | /posts/:id(.:format)      | posts#show
           | PATCH  | /posts/:id(.:format)      | posts#update
           | PUT    | /posts/:id(.:format)      | posts#update
           | DELETE | /posts/:id(.:format)      | posts#destroy

#### Prefix
Prefix加上_path就是Rails的Route helper, 例如想要連去Post index就在view寫上
`<%= link_to posts_path %>`

再來如果看到Prefix為空, 例如如上表create動作Prefix為就空, 就代表Prefix與上一個相同, 但要注意Verb是什麼, link_to預設都是GET, 所以要連去create post view則寫
`<%= link_to posts_path, method: :post %>`

#### URI Pattern
URI Pattern就是網址, 前面就是自己的網域, (.:format)就是要render的格式, 可以是html, JSON等等在controller設定即可就不探討, 預設是html, 之後的表單就省略不寫(.:format)了

再來可以注意到像show的網址有:id這個參數, 就得帶想要看的id進去, 所以要連去第1篇post view則寫
`<%= link_to post_path(1) %>`

帶一個物件也可以, 我們很常在controller看到
```
post = Post.find(1)
redirect_to post_path(post)
```

#### Controller#Action
顧名思義就是找哪個controller的哪個action, 如果有namespace則要注意資料夾結構, 檔案要放對位置

## 嵌套資源(Nested resources)
在post下嵌套comment當範例
```
Rails.application.routes.draw do
  resources :posts do
    resources :comments
  end
end
```
`$ rake routes | grep comments`我們先只看comment的結果

 Prefix              | Verb   | URI Pattern                       | Controller#Action
---------------------|:------:|----------------------------------:| ------------------
 post_comments_index | GET    | /posts/:post_id/comments          | comments#index
                     | POST   | /posts/:post_id/comments          | comments#create
 new_post_comments   | GET    | /posts/:post_id/comments/new      | comments#new
 edit_post_comments  | GET    | /posts/:post_id/comments/:id/edit | comments#edit
 post_comments       | GET    | /posts/:post_id/comments/:id      | comments#show
                     | PATCH  | /posts/:post_id/comments/:id      |  comments#update
                     | PUT    | /posts/:post_id/comments/:id      | comments#update
                     | DELETE | /posts/:post_id/comments/:id      | comments#destroy

照著上一個例子的邏輯沒什麼太大的問題, 唯一要注意的是較好的設計規範
`嵌套資源不要超過一層`
例如這個例子就是有一層了, 盡量不要再往下有resources, 難維護也比較沒意義

## 認識shallow
先理解shallow的設計邏輯:
1. 只有該文章可以列出自己所有的comment, 並且new和create
2. 在任何地方只要能找到該comment就可以show, edit, update, destroy他

只要簡單的寫一行即可做到這個邏輯
```
resources :posts do
  resources :comments, shallow: true
end
```
其效果等同於
```
resources :posts do
  resources :comments, only: [:index, :new, :create]
end
resources :comments, only: [:show, :edit, :update, :destroy]
```
或者這樣比較看得懂
```
resources :posts do
  resources :comments, except: [:show, :edit, :update, :destroy]
end
resources :comments, only: [:show, :edit, :update, :destroy]
```
再來根據前面的例子試著`$ rake routes`結果吧！

## 認識namespace
最常使用的地方是後台的建構, 例如:
```
namespace :admin do
  resources :posts
end
```
`$ rake routes`會發現所有東西都加上了admin

 Prefix          | Verb   | URI Pattern           | Controller#Action
-----------------|:------:|----------------------:| -----------------
 admin_posts     | GET    | /admin/posts          | admin/posts#index
                 | POST   | /admin/posts          | admin/posts#create
 new_admin_post  | GET    | /admin/posts/new      | admin/posts#new
 edit_admin_post | GET    | /admin/posts/:id/edit | admin/posts#edit
 admin_post      | GET    | /admin/posts/:id      | admin/posts#show
                 | PATCH  | /admin/posts/:id      | admin/posts#update
                 | PUT    | /admin/posts/:id      | admin/posts#update
                 | DELETE | /admin/posts/:id      | admin/posts#destroy

比較要注意的是Controller#Action的部分, 此時的controller要放在
`app/controllers/admin/posts_controller.rb`
而檔案裡面class name要改為
```
class Admin::PostsController
end
```

## 漂亮的客製化例如 admin/posts/generator 這種route
利用collection可以蠻方便客製化出想要的route, 但post複數以及單數兩種情況, 如果是複數的話利用即可
```
namespace :admin do
  resource :posts do
    collection do
      get 'generator'
    end
  end
end
```
`$ rake routes | grep generator`結果如下:

 Prefix                | Verb   | URI Pattern            | Controller#Action
-----------------------|:------:|-----------------------:| ------------------
 generator_admin_posts | GET    | /admin/posts/generator | admin/posts#generator

即可快速的創造好複數的route, 但對於覺得generator應該是單數的post的人認為Prefix就會不ok, 比較簡單的單數解法就如下:

```
namespace :admin do
  resource :posts
  resource :post do
    collection do
      get 'generator'
    end
  end
end
```
簡化一點點:
```
namespace :admin do
  resource :posts
  resource :post do
    get 'generator', on: :collection
  end
end
```
`$ rake routes | grep generator`結果如下:

 Prefix               | Verb   | URI Pattern            | Controller#Action
----------------------|:------:|-----------------------:| ------------------
 generator_admin_post | GET    | /admin/post/generator  | admin/post#generator

比較要注意的是Controller#Action要的controller是單數的
`app/controllers/admin/post_controller.rb`

## 小結
當然routes還有很多寫法, 也很容易hack, 利如又想單數又想直接共用複數的controller也可以這樣寫:
```
namespace :admin do
  resource :posts
  resource :post do
    get 'generator', on: :collection, to: 'admin/posts#generator'
  end
end
```
甚至直接一行幹到底:
```
get 'admin/post/generator', to: 'admin/posts#generator', as: 'generator_admin_post'
```
不管想怎麼寫, 保持不斷的rake route查明結果吧!!

參考文章:
書籍 - 為你自己學Ruby on Rails2017.09初版
[Rails 路由：深入淺出](https://rails.ruby.tw/routing.html)

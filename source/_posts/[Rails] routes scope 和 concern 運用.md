---
title: '[Rails]routes scope 和 concern 運用'
date: 2018-08-02 02:26:00
categories:
 - [Rails, Routes]
tags:
 - Rails
 - Routes
---
## scope

最基本產生在某個資料夾的的routes
```ruby
namespace :admin do
  resources :articles
end
```
以index為範例`$ rake routes`可以看出

 method              |verb     |           path  |      controller#method
--------------------|:--------:|----------------:| ---------------------
 admin_articles_path|     GET  | /admin/articles | admin/articles#index

`如果想把 /articles 路徑(path)不帶 /admin 前缀但依然要映射到 Admin::Articles 控制器上，可以用scope`：
```ruby
scope module: 'admin' do
  resources :articles
end
```

得到的index：

 method              |verb      |           path  |      controller#method
---------------------|:--------:|----------------:| ---------------------
        articles_path|     GET  |       /articles | admin/articles#index

還有下面幾種寫法可以得到上面相同的index結果：
```ruby
resources :articles, module: 'admin'
```
```ruby
scope '/admin' do
  resources :articles
end
```

也有一種只改路徑(path)的方法
```ruby
resources :articles, path: '/admin/articles'
```

 method              |verb     |           path  |      controller#method
--------------------|:--------:|----------------:| ---------------------
       articles_path|     GET  | /admin/articles |       /articles#index


## concern

concern其實可以把它當作routes用的變數，宣告後可以給各個地方重複使用，簡單例子：
```ruby
concern :concerntest do
	resources :articles
end
scope module: "admin" do
	concerns :concerntest
end
```
得到的index：

 method              |verb      |           path  |      controller#method
---------------------|:--------:|----------------:| ---------------------
        articles_path|     GET  |       /articles | admin/articles#index

小總結：
如果真的看不懂或不夠資深還是善用`$ rake routes`去把它印出來看，再來我覺得還是不要用太多方法去寫，並不會好維護到哪裡去，以中國有嘻哈的角度看寫起來也不會特別的skr，還是第一眼讓大家看懂最重要。

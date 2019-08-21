---
title: '[Rails]簡單的掌握Routes'
date: 2018-04-12 03:43:00
categories:
 - [Rails, Routes]
tags:
 - Rails
 - Routes
---
相信很多初學者對於Rails的RESTful路徑掌控和資料夾等等一開始感到很複雜，其實不然，善用觀察者指令`rake routes`即可以一覽全局！

``` javascript Routes.rb
Rails.application.routes.draw do
	resources: products

	namespace :admin do
		resources :products
	end

	root 'products#index'
end
```

`resources`會創造出關於CRUD的七大path，分別是
1.index
2.new
3.create
4.show
5.edit
6.update
7.destory
`namespace`可以自定義一個類別
`root`就是指定一進去的首頁該找view底下products這個資料夾裡的index.html.erb

---
resources基於上面原則通常創給resources的controller命名會是複數
例如products

`$ rails g controller products`
搭配model
`$ rails g model product`

這樣我們在define CRUD創立的變數就直覺許多
``` javascript products_controller.rb
def index
  @products = product.all
end

def show
  @product = products.find(params[:id])
end
```
---
`namespace`可以讓網址多一層看起來像
/admin/products

並且會去找controller位於
/admin/products_controller.rb

---
大多時候Routes寫得多要從檔案裡看清楚路徑是很沒效率的，這時候我們在終端機下`$ rake routes`則會有當前的所有路徑，而且以這個資訊來看最準確！
![螢幕快照 2018-04-12 上午11.31.03.png](http://user-image.logdown.io/user/18810/blog/18323/post/7347813/AfwJsdMgRSeaQvH7okj4_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202018-04-12%20%E4%B8%8A%E5%8D%8811.31.03.png)
這邊分成三個欄位

`最左邊的是程式碼的path`，最簡單的例子，只要加上_path就可以用於
<%= link_to ("導向這裡", products__path) %>

`中間欄位就是URL的呈現方式`，:id則是這筆單自己生成的獨立編號，以edit為例
/products/:id/edit(.:format)
在網址上會成為
/products/1/edit

`最右邊的欄位是他會去找哪一個controller執行`，例如
products#show
他會去找
/products/products_controller.rb
裡面的
def show

---

最後隨著專案越來越大Routes也會成長得很快，可以利用簡單得指令過濾出想知道的Controller
`$ rake routes CONTROLLER=products`

參考文章：
[Rails 路由全解](https://ruby-china.github.io/rails-guides/routing.html)

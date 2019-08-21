---
title: '[Rails] routes 高級約束 (Advanced Constraints)'
date: 2018-08-01 10:10:00
categories:
 - [Rails, Routes]
tags:
 - Rails
 - Routes
---
constraints是routes的一個約束方法，官方中文翻譯文件是說約束，但我覺得有點像是專屬於routes的if，如果成立才可以執行，例如最簡單的兩個例子：

```ruby
match 'photos', to: 'photos#show', via: [:get, :post]
```
只要http verb不是指定的get或post就不行了

```ruby
get 'photos/:id', to: 'photos#show', constraints: { id: /[A-Z]\d{5}/ }
```
一個可以利用正則去驗證id的概念

再來高級約束有什麼厲害的地方？
官方提供了一個很酷的例子，擋ip

```ruby
class BlacklistConstraint
  def initialize
    @ips = Blacklist.retrieve_ips
  end

  def matches?(request)
    @ips.include?(request.remote_ip)
  end
end
 
Rails.application.routes.draw do
  get '*path', to: 'blacklist#index', constraints: BlacklistConstraint.new
end
```
他的遊戲規則是`constraints一定會去跑class的matches?得到一個bool來決定可不可以通過該routes`。

而這個範例利用了class.new的特性先去自動執行initialize得到一個instance value(@ips)然後再自動去執行matches?

request則是內建的物件有很多方法可以用，remote_ip則是其中一個。

而routes設定get ‘*path’(所有路徑)的時候都會觸發，所以remote_ip有包含在Blacklist.retrieve_ips裡面則routes會把你導去啟動blacklist#index黑名單的controller，十分的酷炫。


為什麼研究到這個是因為公司的例子，一個rails專案裡面放兩個產品專案，然後利用routes偵測subdomain去決定要顯示什麼頁面：

```ruby
…(略)
constraints(Subdomain::B) do
  get “/welcome_b”  => “welcome_b#index”
end
…(略)
```

```ruby
module Subdomain
  class A
    def self.matches?(request)
      request.subdomain.in?  ["www", "staging", ""]
    end
  end

  class B
    def self.matches?(request)
      request.subdomain.in? [“B”, “B-staging"]
    end
  end
end
```
所以直接偵測網址的subdomain裡有出現陣列字串就可以成立，那現在出現了一個問題，我要怎麼開發B網站呢？

localhost預設是一個沒有subdomain的domain，而沒有subdomain所以subdomain等於空字串是成立的，所以把A網站陣列裡的空字串改到B的陣列就可以嘍！

參考文章：
[Rails Guides英文](https://guides.rubyonrails.org/routing.html#advanced-constraints)
[Rails Guides中文](https://ruby-china.github.io/rails-guides/routing.html#advanced-constraints)
兩天同一篇

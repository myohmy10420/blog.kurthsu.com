---
title: '[Rails] 簡介 before, after and around actions(filters)'
date: 2020-05-27 18:29:40
categories:
 - [Rails]
tags:
 - Rails
 - before_action
 - after_action
 - around_action
 - filters
---

最近在看 I18n 的文件, 突然看到了 around_action, 然後才發現我對這塊的理解不到這麼熟悉所以記錄一下, 搭配官方文件和一篇外國文章就能解釋得非常清楚了, 那篇外國文章範例非常好, 基本上我就直接沿用他的範例了~

## 關鍵字 active controller filter

在 rails action_controller 的官方文件裡面, before_action 那些都稱為 filter, 不過查詢 before_action 或者 before_filter 都可以找到很多資訊, filter 是舊的 helper 名稱在 rails v2.3.8 已經被遺棄了

> (所有 filters 官方文件)[https://guides.rubyonrails.org/action_controller_overview.html#filters], 就是現在的 before_action 那些
> (before_filter 官方文件)[https://apidock.com/rails/ActionController/Filters/ClassMethods/before_filter], 名稱在 rails v2.3.8 已經被遺棄

`接下來 before_action, after_action 等等都統稱為 filters`

## before_action

剪貼一些官方敘述和自己的見解

- If a "before" filter renders or redirects, the action will not run. If there are additional filters scheduled to run after that filter, they are also cancelled.
> 如果在 before_action 提早 renders 或 redirects, 接下來所有的 action 或 filters 都會被中斷不會執行
- Calling the same filter multiple times with different options will not work, since the last filter definition will overwrite the previous ones.
> 同名 filter 的 options(only, except等) 會覆蓋掉前一個的

## after_action

剪貼一些官方敘述和自己的見解

- "after" filters cannot stop the action from running.
> after_action 不能中斷 action, 畢竟是 action 成功執行完之後才會跑 after_action
- "after" filters are executed only after a successful action, but not when an exception is raised in the request cycle.
> after_action 一定要在 action 成功執行完之後才會執行, 如果在 action except 或 raise 掉了也不會執行

## around_action

剪貼一些官方敘述和自己的見解

- "around" filters are responsible for running their associated actions by yielding, similar to how Rack middlewares work.
> around_action 可以像 Rack middlewares 層一樣處理執行 action 前或後的流程, 在 around_action block 裡面用 yield 執行 action
- Note that an "around" filter also wraps rendering.
> around_action 會包含 rendering, 也就是說即使 action call render 也會把剩下的程式碼執行完畢
> 也就是說 yield 之前的 code 都像是 before_action, yield 之後的 code 都像是 after_action

## 一些執行 filter 的方式

### 常見 filter 執行方式

```ruby
before_action :method_in_controller

def method_in_controller
  puts 'Run filter'
end
```

### 直接執行 filter 的方式

```ruby
before_action do |controller|
  controller.send(:method_in_controller)
end
before_action { |controller| controller.send(:method_in_controller) }

around_action do |controller, action|
  controller.send(:method_in_controller)
  action.call
end

def method_in_controller
  puts 'Run filter'
end
```

注意因為 filter 執行的 scope 不是在 controller 裡面, 所以要用傳進來的 controller 變數 send controller 的方法

不過除非要處理的事情真的極其簡單, 不然不建議這種寫法

## 注意 block argument

要注意一下 call method 方式的參數, action 前面要加`&`代表他是個 block, 比較兩個差異
```ruby
around_action do |controller, action|
  controller.send(:method_in_controller)
  action.call
end

around_action :other_method_in_controller

def other_method_in_controller(&action)
  method_in_controller
  action.call # same as yield
end
```

## 實作範例 & 執行順序

```ruby
class ApplicationController < ActionController::API
  before_action { puts "Calling before_action 1" }
  before_action { puts "Calling before_action 2" }

  around_action do |controller, block|
    puts "Calling around_action 1 - before yield"
    block.call
    puts "Calling around_action 1 - after yield"
  end
  around_action do |controller, block|
    puts "Calling around_action 2 - before yield"
    block.call
    puts "Calling around_action 2 - after yield"
  end

  after_action { puts "Calling after_action 1" }
  after_action { puts "Calling after_action 2" }
end

class PagesController < ApplicationController
  def test
    puts "Executing action"
    head :ok
  end
end

# after excute PagesController#test will output below:
# =>
# Calling before_action 1
# Calling before_action 2
# Calling around_action 1 - before yield
# Calling around_action 2 - before yield
# Executing action
# Calling after_action 2
# Calling after_action 1
# Calling around_action 2 - after yield
# Calling around_action 1 - after yield
```

會發現兩個現象:
1. 越晚出現的 before_action 越晚執行, 包括 around_action yield 前的程式碼
2. 越晚出現的 around_action 越早執行, 包括 around_action yield 後的程式碼

### 繼承的執行順序

現在 PagesController 也自己也有 filters:

```ruby
class PagesController < ApplicationController
  before_action -> { puts "Calling before_action 3" }
  after_action -> { puts "Calling after_action 3" }
  # ...same
end

# after excute PagesController#test will output below:
# =>
# Calling before_action 1
# Calling before_action 2
# Calling around_action 1 - before yield
# Calling around_action 2 - before yield
# Calling before_action 3
# Executing action
# Calling after_action 3
# Calling after_action 2
# Calling after_action 1
# Calling around_action 2 - after yield
# Calling around_action 1 - after yield
```

父 class 的東西都先加入排程, 其餘就跟剛剛的現象一樣

## prepend filters

比 before_action 更高的權重就是有前綴 prepend, 這個 filter 會先執行, 也有三種:
- prepend_before_action
- prepend_after_action
- prepend_around_action

更新剛剛的 PagesController 來試試看:

```ruby
class PagesController < ApplicationController
  prepend_before_action -> { puts "Calling before_action 3" }
  prepend_after_action -> { puts "Calling after_action 3" }
  # ...same
end

# after excute PagesController#test will output below:
# =>
# Calling before_action 3
# Calling before_action 1
# Calling before_action 2
# Calling around_action 1 - before yield
# Calling around_action 2 - before yield
# Executing action
# Calling after_action 2
# Calling after_action 1
# Calling around_action 2 - after yield
# Calling around_action 1 - after yield
# Calling after_action 3
```

兩個特性注意:
1. 有前綴的 filters 順序跟沒有前綴的一樣
2. 有前綴的 filters before 會更先執行, 反之 after 會更後執行

## 更新順序

如先前提到的重複寫 filters 會有兩個行為:
1. 只會執行一次
2. 最後的會排程會覆蓋掉前面的

所以除了在同一個 controller 可以排執行順序之外也可以利用繼承的特性去排列:

```ruby
class ApplicationController < ActionController::API
  before_action :before_1
  before_action :before_2
  before_action :before_3

  private

  def before_1
    puts "Calling before_action 1"
  end

  def before_2
    puts "Calling before_action 2"
  end

  def before_3
    puts "Calling before_action 3"
  end
end

class PagesController < ApplicationController
  before_action :before_1
  before_action :before_3
  # ...same
end

# after excute PagesController#test will output below:
# =>
# Calling before_action 2
# Calling before_action 1
# Calling before_action 3
```

最後執行 PagesController#test 時, 同名的 filters 會覆蓋掉先前 ApplicationController 同名 filters 的排程

## 小結

充分的了解 filters 後, 我覺得專案裡還是不要有過多的 filters, 不然有些狀況會顯得很通靈, 而且真的會很難除錯, 要一層一層 class 把 filters 都印出來看, 如果真的要寫這麼多建議寫好測試

再來可以了解到如果程式碼是 filters 有問題, 如果連 action 一開始都 binding 跑步進去的話肯定是 before_action 或者 around_action 有問題, 因為只有這兩個可以中斷 action 執行

## 參考文章

[官方文件 filters](https://guides.rubyonrails.org/action_controller_overview.html#filters)
[Ordering of Filters in Rails Controllers](https://reganchan.ca/blog/ordering-of-filters-in-rails-controllers/)

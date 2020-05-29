---
title: '[Rails] I18n 語系設定'
date: 2020-05-27 10:32:29
categories:
 - [Rails, i18n]
tags:
 - Rails
 - i18n
---

記錄一下 I18n 設定和轉換語系的方式, 至少看得懂專案裡 Rails 如何使用 I18n 的也挺重要

## config 基本設定

```ruby config/application.rb
module YourRepo
  class Application < Rails::Application
    # 設定從哪裡讀取 .yml 翻譯檔
    config.i18n.load_path += Dir[Rails.root.join('lib', 'locale', '*.{rb,yml}')]
    # 允許哪些語系
    config.i18n.available_locales = [:en, :zh]
    # 預設語系, 不設定的話 default 是 :en
    config.i18n.default_locale = :zh
  end
end
```

## locale vs with_locale

locale 和 with_locale 都可以改變當前的語系, locale 還可以拿到當前語系, 先基本了解一下他們的特性:

```ruby
I18n.locale #=> :en

I18n.with_locale(:fr) do
  I18n.locale #=> :fr
end

I18n.locale #=> :en
```

官方的敘述如下:
```
I18n.locale can leak into subsequent requests served by the same thread/process if it is not consistently set in every controller. For example executing I18n.locale = :es in one POST requests will have effects for all later requests to controllers that don't set the locale, but only in that particular thread/process. For that reason, instead of I18n.locale = you can use I18n.with_locale which does not have this leak issue.
```
意思大概是說, 用 I18n.locale 設定語系的話之後的`『一些』` requests 會是該語系, 但執行幾個 requests 之後又會變成 default_locale, 這會延伸一個問題會在主題『利用 cookie 轉換語系紀錄』

## 利用 URL query + cookie 轉換語系

我們可以在 html 用 link_to 帶上參數去轉換語系, html 如下:
```html
<%= link_to '中文版', locale_change_path(locale: 'zh') %>
<%= link_to 'English', locale_change_path(locale: 'en') %>
```

再來在 controller 裡面:
```ruby locale_controller.rb
class LocaleController < ApplicationController
  def change
    if params[:locale] && I18n.available_locales.include?( params[:locale].to_sym )
      session[:locale] = params[:locale]
    end

    I18n.locale = session[:locale] || I18n.default_locale
    redirect_to root_path
  end
end
```

這時候雖然發現成功改變了語系, 但只要重新刷新或導向幾次頁面之後就會發現語系又回到 default_locale 了, 這就是剛剛在主題『locale vs with_locale』最後提到的問題, 簡單的解決方法就做一個 around_action 一直保持語系正確即可, 通常在 application_controller 做:
```ruby application_controller.rb
class ApplicationController < ActionController::Base
  around_action :switch_locale

  def switch_locale(&action)
    locale = session[:locale] || I18n.default_locale
    I18n.with_locale(locale, &action)
  end
end
```

## 利用 URL Domain 切換語系

例如有以下 Domain 並指定指定語系

- blog.kurthsu.com => en
- blog.kurthsu.tw  => zh-TW
- blog.kurthsu.jp  => jp

```ruby application_controller.rb
class ApplicationController < ActionController::Base
  around_action :switch_locale

  LOCALE_LIST = {
    'com' => :en,
    'tw'  => 'zh-TW',
    'jp'  => :jp
  }

  def switch_locale(&action)
    locale = parsed_locale_from_tld || I18n.default_locale
    I18n.with_locale(locale, &action)
  end

  def parsed_locale_from_tld
    LOCALE_LIST[request.host.split('.').last]
  end
end
```

最後如果是連到中文版的 link_to 改為:
```html
<%= link_to '中文版', "blog.kurthsu.tw#{request.env['PATH_INFO']}" %>
```

不過這感覺要用點財力購買完整系列的 domain, 雖然我想也是可以用 CNAME 做些處理

## 利用 URL path 切換語系

這邊將利用 routes 切分指定語系並且用 books 當範例

- blog.kurthsu.com/en/books => en
- blog.kurthsu.com/zh-TW/books => zh-TW

先設定 default_url_options 在 ApplicationController
```ruby application_controller.rb
class ApplicationController < ActionController::Base
  around_action :switch_locale

  def switch_locale(&action)
    locale = parsed_locale_from_tld || I18n.default_locale
    I18n.with_locale(locale, &action)
  end

  def default_url_options
    { locale: I18n.locale }
  end
end
```

這邊會先執行 around_action, 如果目前的 I18n.locale 為 en
此時所有的 URLs 路徑都會包含 query => `?locale=en`, 例如:
- blog.kurthsu.com/?locale=en
- blog.kurthsu.com/books?locale=en

再來更新 routes, 利用網址可以挖洞給 params 的特性把網址變漂亮
```ruby routes.rb
Rails.application.routes.draw do
  root to: 'root_path#index'

  scope path: '/:locale' do
    resources :books
  end
end
```

網址除了 root_path 成功的變成我們要的:
- blog.kurthsu.com/?locale=en
- blog.kurthsu.com/en/books

也可以用 option 的寫法:
```ruby routes.rb
Rails.application.routes.draw do
  root to: 'root_path#index'

  scope path: '(:locale)', locale: /en|zh-TW/ do
    resources :books
  end
end
```
這樣如果網址像下面這樣缺少 locale 也不會噴 Routing Error, 然後會採用 I18n.default_locale
- blog.kurthsu.com/books

最後 root_path 也想變漂亮變成:
- blog.kurthsu.com/en
- blog.kurthsu.com/zh-TW
在 root to: 前面新增一個 route
```ruby routes.rb
Rails.application.routes.draw do
  get '/:locale', to: 'root_path#index'
  root to: 'root_path#index'

  scope path: '(:locale)', locale: /en|zh-TW/ do
    resources :books
  end
end
```
但 link_to root_path 回傳的網址依然會是
- /?locale=en
- /?locale=zh-TW

最後小提醒這種方式最好一直保持 I18n.locale 的乾淨不要去頻繁的改變它, 想用 helper 產生指定語系的網址直接給參數即可:
- books_path(locale: 'en')
- books_path(locale: 'zh-TW')

## 小結

還有一些方法, 例如 http header 或者把使用者的語系存在欄位裡面以便登入時可以切換適用於他的語系, 總之可以在範例中 `around_action :switch_locale` 判斷該用哪個語系即可

以前比較常用『URL query + cookie 轉換語系』這招但官方不建議了, 因為同一個網址複製過去給別人看到的語系可能會不同, 看到的東西也就不同, 『URL path 切換語系』則不會有這個問題, 而且簡單又沒什麼成本, 蠻推薦的

## I18n 系列:
{% post_link '[Rails] I18n 基本使用' '[Rails] I18n 基本使用]' %}
{% post_link '[Rails] I18n default scope' '[Rails] I18n default scope]' %}

## 參考文章
[官方文件 Managing the Locale across Requests](https://guides.rubyonrails.org/i18n.html#managing-the-locale-across-requests)

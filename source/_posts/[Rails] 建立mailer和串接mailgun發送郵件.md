---
title: '[Rails]建立mailer和串接Mailgun發送郵件'
date: 2018-05-04 08:49:00
categories:
 - [Rails, mailer]
 - [Mailgun]
tags:
 - Rails
 - mailer
 - Mailgun
---
參考文章：
[Rails 實戰聖經](https://ihower.tw/rails/actionmailer.html)
[Rails Guides](https://rails.ruby.tw/action_mailer_basics.html)

## 前言
---
我們在創造Rails專案的時候其實已經有下列檔案：
`app/mailsers/application_mailer.rb`
`app/views/mailer.html.erb`
`app/views/mailer.text.erb`

基本上跟原本的網頁東西原理很像
`app/mailsers`裡面的東西就像是controller
`app/views/mailer.html.erb`就像是網頁的application.html.reb
`app/views/mailer.text.erb`則是因為不是所有客戶端都可以顯示 HTML 格式的信件所以有存文字檔

## 建立一個最簡單的mailer
---
`$ rails generate mailer UserMailer`
創造了：
`app/mailers/user_mailer.rb`
`app/views/user_mailer`
以及一些test檔

先修改主要的application_mailer檔案：
``` ruby app/mailers/application_mailer.rb
class ApplicationMailer < ActionMailer::Base
  default from: "from@example.com"
  layout 'mailer'
end
```
`default`為設定寄信人的信箱

再來創造一個最簡單的welcome_email

``` ruby app/mailers/user_mailer.rb
class UserMailer < ApplicationMailer
  def welcome_email(email)
    @email = email
    mail(to: @email, subject: 'Welcome to My Awesome Site')
  end
end
```

這時候剛剛創造的method是什麼名稱就在`app/views/user_mailer`資料夾創造一樣名稱的html：
`$ touch app/views/user_mailer/welcome_email.html.erb`

簡單做出html：
``` html app/views/user_mailer/welcome_email.html.erb
<!DOCTYPE html>
<html>
  <head>
    <meta content='text/html; charset=UTF-8' http-equiv='Content-Type' />
  </head>
  <body>
    <h1>Welcome!!!</h1>
    <p>
      You have successfully signed up here
    </p>
    <p>Thanks for joining and have a great day!</p>
  </body>
</html>
```

再來可以先嘗試在rails console看有沒有東西：
`$ rails c`
`$ UserMailer.welcome_email("example@test.com")`

此時如果真的用送出去的指令：
`$ UserMailer.welcome_email("example@test.com").deliver!`
則會報錯

由於在本地端永遠不會真的送出郵件，利用letter_opener這個gem可以模擬信件在遊覽器上面
``` ruby Gemfile
gem 'letter_opener', group: :development
```
`$ bundle install`

修改config/environments/development.rb環境檔：
``` ruby config/environments/development.rb
…(略)
  config.action_mailer.default_url_options = { host: 'localhost:3000' }
  config.action_mailer.delivery_method = :letter_opener
end
```
`$ rails c`
`$ UserMailer.welcome_email("example@test.com").deliver!`
應該可以成功看到模擬畫面！

## 串接mailgun
---
#### 我的專案是利用Capistrano部署在linnode上

註冊完mailgun後他會給一個免費的domain帶有亂碼sandboxdxxxx....mailgun.org，打開之後有以下訊息：
- IP Address
- SMTP Hostname
- Default SMTP Login
- API Base URL
- Default Password
- API Key

修改production.rb：
``` ruby config/environments/production.rb
…(略)
  config.action_mailer.default_url_options = { :host => '你的网域名称，暂时还没有就填IP地址' }
  config.action_mailer.delivery_method = :smtp
  config.action_mailer.smtp_settings = config_for(:email).symbolize_keys
end
```

mailer會需要mailer.yml這隻檔案，但為了避免自己的密碼被看到，創造一個空的檔案：
`$ touch config/email.yml`

然後修改deploy.rb檔案：
``` ruby deploy.rb
append :linked_files, "config/database.yml", "config/secrets.yml", "config/email.yml"
```
代表說等等他會去伺服器上面路徑`~/jdstore/shared/config/`裡面找這些檔案

到伺服器上：
`$ touch 專案名稱/shared/config/email.yml`
輸入以下資訊：
``` ruby email.yml
production:
  address: "smtp.mailgun.org"
  port: 587
  domain: "mailgun.org"
  authentication: "plain"
  user_name: "postmaster@sandboxXXXXXX.mailgun.org"
  password: "XXXXX"
  enable_starttls_auto: true
```

`user_name`替換成mailgun上的Default SMTP Login
`password`替換成mailgun上的Default Password

回到本地端commit一版push到github上面後deploy
`$ cap production deploy`

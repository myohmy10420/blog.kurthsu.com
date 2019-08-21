---
title: '[Rails]Devise login with google'
date: 2018-04-19 14:41:00
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - Devise
---
參考文件
[OmniAuth Google OAuth2](Strategy https://github.com/zquestz/omniauth-google-oauth2)
[Login With Google Account Using Devise and Omniauth](https://viblo.asia/p/login-with-google-account-using-devise-and-omniauth-PwRGgWAPGEd)
打開官方文件參考從Devise開始的部分

##安裝及申請
---
安裝gem
``` javascript Gemfile
gem 'omniauth-google-oauth2'
```
`$ bundle install`

1.到google console https://console.developers.google.com頁面
2.頁面最上面創立一個專案
3.啟用兩個API服務`Contacts API`以及`Google+ API`
4.左邊的選單選擇憑證申請OAuth 2.0 用戶端 ID
5.可能需要等十分鍾

##Devise的方式實作
---
這邊假設已經安裝好Devise並且已經用Devise創造出users這個model

修改config/initializers/devise.rb
``` javascript config/initializers/devise.rb
config.omniauth :google_oauth2, 'GOOGLE_CLIENT_ID', 'GOOGLE_CLIENT_SECRET', {}
```
`GOOGLE_CLIENT_ID`填入剛剛申請的用戶ID
`GOOGLE_CLIENT_SECRET`填入剛剛申請的用戶密碼

這時候登入頁面最下面應該已經多一個Sign in with GoogleOauth2的連結，且登入成功後會預設去users/omniauth_callbacks這個path！

修改routes.rb加上如果google登入成功後會呼叫的path
``` javascript routes.rb
devise_for :users, controllers: { omniauth_callbacks: 'users/omniauth_callbacks' }
```

再來更新model新增一行
``` javascript /app/models/user.rb
devise :omniauthable, omniauth_providers: [:google_oauth2]
```

再來不要忘記controller
你可以放在controller/application.rb或自行創立對應路徑
`rails g controller users/omniauth_callbacks`可以創造對應的路徑檔案

``` javascript omniauth_callbacks_controller.rb
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
  def google_oauth2
      # You need to implement the method below in your model (e.g. app/models/user.rb)
      @user = User.from_omniauth(request.env['omniauth.auth'])

      if @user.persisted?
        flash[:notice] = I18n.t 'devise.omniauth_callbacks.success', kind: 'Google'
        sign_in_and_redirect @user, event: :authentication
      else
        session['devise.google_data'] = request.env['omniauth.auth'].except(:extra) # Removing extra as it can overflow some session stores
        redirect_to new_user_registration_url, alert: @user.errors.full_messages.join("\n")
      end
  end
end
```
此時注意到controller裡面`from_omniauth`這個方法不是內建的要自行創立

再次更新model:
``` javascript /app/models/user.rb
def self.from_omniauth(access_token)
  data = access_token.info
  user = User.where(:email => data["email"]).first

  unless user
    password = Devise.friendly_token[0,20]
    user = User.create(email: data["email"],
      password: password, password_confirmation: password
    )
  end
  user
end
```
從unless開始是我自己的程式碼可以參考
給password獨特的token
然後我只記下使用者的信箱

意思是我利用google信箱登入後如果有在我的資料庫找到該位user就直接用，沒有的話就創立一個user。

此時應該就大功告成了！

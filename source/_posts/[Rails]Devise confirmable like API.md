---
title: '[Rails]Devise confirmable'
date: 2020-05-26 11:23:23
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - Devise
---

記錄一下自己實作 devise confirmable 基本功能
將會做到:
1. 註冊完之後發驗證信, 並且驗證完後才能正常登入
2. 客製化 devise controller
3. 提前阻擋 warden 這個 gem 的 error respond 處理

## 參考環境

記錄一下自己的版本, 也順便檢查有無安裝到相關 gem:
```ruby Gemfile
ruby '2.6.3'

gem 'rails', '~> 6.0.2', '>= 6.0.2.2'

group :development do
  gem 'letter_opener'
end
```

```ruby Gemfile.lock
  rails (6.0.2.2)
  letter_opener (1.7.0)

RUBY VERSION
   ruby 2.6.3p62

BUNDLED WITH
   2.0.2
```

我的 model 是 `User`

## model 新增 confirmable

migration 新增 confirmable 相關欄位
```ruby
class AddConfirmableToUsers < ActiveRecord::Migration[6.0]
    def change
        add_column :users, :confirmation_token, :string
        add_column :users, :confirmed_at, :datetime
        add_column :users, :confirmation_sent_at, :datetime
        # add_column :users, :unconfirmed_email # Only if using reconfirmable
    end
end
```

官方欄位解說和自己見解
- confirmation_token   - A unique random token
> 只要發送驗證信該 User 會更新這個欄位, 驗證信上的網址會帶上這個 token 找到該 User 然後做驗證
- confirmed_at         - A timestamp when the user clicked the confirmation link
> 該 User 有沒有驗證過是看這個欄位有沒有值
- confirmation_sent_at - A timestamp when the confirmation_token was generated (not sent)
> 可以設定 token 有效期限
- unconfirmed_email    - An email address copied from the email attr. After confirmation this value is copied to the email attr then cleared
> User 修改 E-mail 可以做重新認證, 這次沒有用到這個功能所以也沒新增這個欄位

更新 User model
```ruby user.rb
class User < ApplicationRecord
  devise :confirmable
end
```

## 建立 controller 以及 更新 route

使用 devise 提供的指令建立 controller
`$ rails generate devise:controllers users -c=confirmations`
將為產生`app/controllers/api/v1/users/confirmations_controller.rb`, 且提示要更新 routes 的訊息如下:
```ruby config/routes.rb
Rails.application.routes.draw do
  devise_for :users, controllers: {
    sessions: 'users/sessions'
  }
end
```

也可以自己規劃資料夾, 例如想把 devise 的 controller 都規劃到 API 底下的話, 把剛剛 devise 產生的資料夾移動到 API 資料夾結構底下, 並且更新 controller 和 routes

```ruby app/controllers/api/v1/users/confirmations_controller.rb
module Api
  module V1
    module Users
      class ConfirmationsController < Devise::ConfirmationsController
        # Lots of methods and code there...
      end
    end
  end
end
```

```ruby config/routes.rb
Rails.application.routes.draw do
  root to: 'page#landing'

  devise_for :users, controllers: {
    confirmations: 'api/v1/users/confirmations'
  }
end
```

再來我要做的 confirmable 非常簡單, 以下幾點:
1. 點信件連結登入後認證 User 並且登入導回首頁
2. 認證過的 User 會提示已認證過並且登入導回首頁
3. 其餘的錯誤由 flash 呈現並且導回首頁

最後 controller 只會用到 show 這個 method 所以 overwrite show 即可:
```ruby app/controllers/api/v1/users/confirmations_controller.rb
module Api
  module V1
    module Users
      class ConfirmationsController < Devise::ConfirmationsController
        def show
          self.resource = resource_class.confirm_by_token(params[:confirmation_token])

          # Devise adds errors if fail confirm by token
          if resource.errors.empty?
            set_flash_message!(:notice, :confirmed)
            sign_in(self.resource)
          # Ignore other errors if already confirmed
          elsif resource.confirmed?
            set_flash_message!(:notice, 'errors.messages.already_confirmed')
            sign_in(self.resource)
          else
          # Show devise's other oringin errors
            errors = self.resource.errors.full_messages.join("\n")
            set_flash_message!(:notice, errors)
          end

          redirect_to root_path
        end
      end
    end
  end
end
```

## 設定 letter_opener 做測試

最後 devise 在註冊成功的時候即會自動發郵件, 可以用 letter_opener 模擬打開認證信件然後做測試, Gemgile 安裝好之後做以下設定:
```ruby config/environments/development.rb
Rails.application.configure do
# ... lots of codes
  config.action_mailer.delivery_method = :letter_opener
  config.action_mailer.perform_deliveries = true
end
```

重新打開 server 之後就可以註冊一個新帳號試試看剛剛的所有設定有沒有成功了

## 登入後註冊 console 會噴 302 error (warden 問題)

devise 不允許在登入後再做註冊的動作, 這時候會回傳 302 error 掉並且跳轉, 所以最簡單解是在自己的 registrations_controller.rb 加上 prepend_before_action 檢查是否已經登入:
```ruby your_path_to/registrations_controller.rb
class RegistrationsController < Devise::RegistrationsController
  prepend_before_action :already_signed_in, only: [:create]
  #...lots of codes
  private

  def already_signed_in
    return if !current_user

    # Or your can add a flash and redirect_to other place
    render json: "Already signed in", status: 302
  end
end
```

發生的原因是因為 Devise::RegistrationsController 會做一個 `prepend_before_action :require_no_authentication, only: [:new, :create, :cancel]` 的動作, 他被定義在 `DeviseController`, 然後執行到 `warden.authenticate?(*args)` 這段的時候會回傳 302 error 掉並且跳轉, 所以我又再加一個比他更前面的動作 prepend_before_action 去驗證是否已經登入並且做自己想要的流程

## 可以不用先驗證

如果想先給會員註冊完馬上使用一些不用一定要先驗證的功能可以在 devise config 新增:
```ruby config/initializers/devise.rb
Devise.setup do |config|
  config.allow_unconfirmed_access_for = nil
end
```

這個參數可以設定天數, 例如設定
`config.allow_unconfirmed_access_for = 2.days`
意思是這兩天內會員還可以不用先驗證, 如果設定 nil 代表永遠都可以不用先驗證
而要用到需要驗證功能的時候可以在 controller 用 `current_user.confirmed?` 去做判斷

## 手動發送驗證信

預設註冊成功會有發送信件的行為, 如果想把這個行為改掉用手動發送的話就在 controller 裡執行:
```ruby
current_user.send_confirmation_instructions
```

## 略過驗證/直接驗證

這邊是指直接讓該 user 通過驗證, 因為英文不好一開始我還以為在做剛剛提到的『可以不用先驗證』的事情, 兩個是完全不一樣的, 看以下範例, 假設我註冊了之後還沒驗證
```ruby
some_user = User.last
puts some_user.confirmed?   # => false
puts some_user.confirmed_at # => nil
some_user.skip_confirmation!
puts some_user.confirmed?   # => true
puts some_user.confirmed_at # => some date time
```

## 小結

這篇主要是野生的(自己實作遇到問題自己解), 相信團隊裡資深的成員都有更完善更好的處理方法

我實作的是基本的 confirmable, 還有其他功能 reconfirmable 沒有做到, 不過官方慢慢挖應該都有 devise 算是還蠻好挖慢慢看就看得懂的 gem, 只是要注意一些 warden 的動作, 有時候他會直接回傳錯誤然後直接導向感覺很像通靈, 我是 monkey patch 一下才知道原來是他搞的鬼

簡單處理 warden 自己回傳錯誤然後直接導向的問題可以先下個關鍵字 `devise CustomFailureApp`, 基本的狀況和用法在另外寫一篇記錄

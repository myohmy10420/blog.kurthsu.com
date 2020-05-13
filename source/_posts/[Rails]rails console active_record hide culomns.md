---
title: '[Rails]rails console active_record hide culomns'
date: 2020-05-13 19:12:33
categories:
 - [Rails]
tags:
 - Rails
 - ActiveRecord
---

突然很好奇 devise 如何在 rails console 裡面隱藏 columns, 例如在 schema 裡面 users 這個 table 明明有 encrypted_password 這個欄位為什麼 console 不會出現呢?

##  ActiveRecord::Relation#inspect

首先了解我們在 console 撈 ActiveRecord 為什麼會回傳以下的東西
```
$ User.first
#<User id: 1, email: "test@test.com", created_at: "2020-05-08 09:18:36", updated_at: "2020-05-08 09:18:36">
```

原來是在 ActiveRecord::Relation#inspect 這個 method 做事情
``` ruby
def inspect
  subject = loaded? ? records : self
  entries = subject.take([limit_value, 11].compact.min).map!(&:inspect)

  entries[10] = "..." if entries.size == 11

  "#<#{self.class.name} [#{entries.join(', ')}]>"
end
```

而 devise 這個 gem 在 module Authenticatable 定義如下:
``` ruby
BLACKLIST_FOR_SERIALIZATION = [
  :encrypted_password,:reset_password_token, :reset_password_sent_at,
  :remember_created_at,
  :sign_in_count,
  :current_sign_in_at,
  :last_sign_in_at,
  :current_sign_in_ip,
  :last_sign_in_ip,
  :password_salt,
  :confirmation_token,
  :confirmed_at,
  :confirmation_sent_at,
  :remember_token,
  :unconfirmed_email,
  :failed_attempts,
  :unlock_token,
  :locked_at
]

def serializable_hash(options = nil)
  options = options.try(:dup) || {}
  options[:except] = Array(options[:except])

  if options[:force_except]
    options[:except].concat Array(options[:force_except])
  else
    options[:except].concat BLACKLIST_FOR_SERIALIZATION
  end

  super(options)
end

def inspect
  inspection = serializable_hash.collect do |k,v|
    "#{k}: #{respond_to?(:attribute_for_inspect) ? attribute_for_inspect(k) : v.inspect}"
  end
  "#<#{self.class} #{inspection.join(", ")}>"
end
```

當然可以在任一 model 重新定義他, 即使隨意修改另一個字串也依然可以對 User.last 這個 ActiveRecord object 正常操作, 這個只是在撈資料的時候回傳一個好讀的字串而已, 官方的解釋如下:
`Returns the contents of the record as a nicely formatted string.`

## 那如何看到全部 column 以及 value 呢

簡單的下 attributes methods 即可:
```
$ User.last.attributes
{"id"=>1, "email"=>"test@test.com", "encrypted_password"=>"xxxxx", "reset_password_token"=>nil, "reset_password_sent_at"=>nil, "remember_created_at"=>nil, "confirmation_token"=>"xxxxx", "confirmed_at"=>nil, "confirmation_sent_at"=>Fri, 08 May 2020 09:18:36 UTC +00:00, "unconfirmed_email"=>nil, "created_at"=>Fri, 08 May 2020 09:18:36 UTC +00:00, "updated_at"=>Fri, 08 May 2020 09:18:36 UTC +00:00}
```
只是要注意這個就是一個 hash 嘍!

## 只想在某一個 API 像這樣 hide 一些欄位呢?

我們可以用去除法(except)
```ruby
User.first.serializable_hash({except: [:email, :id]}).to_json
```

只想取某幾個欄位的方法(only)
```ruby
User.first.serializable_hash({only: [:email, :id]}).to_json
```

甚至可以取 nested 的欄位
```ruby
# user has_many notes
User.first.serializable_hash(include: { notes: { only: 'title' }})
```

## 參考文章
[Rails官方 for inspect](https://apidock.com/rails/ActiveRecord/Relation/inspect)
[Rails官方 for serializable_hash](https://apidock.com/rails/ActiveModel/Serialization/serializable_hash)
[Stackoverflow - Rails 5 model objects not showing all Devise attributes](https://stackoverflow.com/questions/39632699/rails-5-model-objects-not-showing-all-devise-attributes)
[Stackoverflow - Rails & Devise: devise specific columns not showing up in rails console](https://stackoverflow.com/questions/41754087/rails-devise-devise-specific-columns-not-showing-up-in-rails-console)

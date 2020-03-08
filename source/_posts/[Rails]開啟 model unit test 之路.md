---
title: '[Rails]開啟 model unit test 之路'
date: 2020-03-07 16:40:43
categories:
 - [Rails, RSpec]
tags:
 - Rails
 - RSpec
---

最近練習了一些unit test, 這邊直接用一個新專案紀錄基本備置和用法

# 環境

- Rails 5.2.4
- Ruby 2.4.4

# 安裝的 gem & 注意事項

- [rspec-rails](https://github.com/rspec/rspec-rails)
- [factory_bot_rails](https://github.com/thoughtbot/factory_bot_rails)
- [simplecov](https://github.com/colszowka/simplecov)

先設定 Gemfile
``` ruby Gemfile
group :development, :test do
  gem 'pry'
  gem 'rspec-rails'
  gem 'factory_bot_rails'
end

group :test do
  gem 'simplecov', require: false
end
```

`$ bundle install`
`$ rails generate rspec:install` -> 會多spec_helper.rb
設定SimpleCov.start在建立測試環境時越早載入越好
在configure引入FactoryBot所有Methods

``` ruby rails_helper.rb
require 'spec_helper'
ENV['RAILS_ENV'] ||= 'test'
require 'simplecov'
SimpleCov.start

#... 略

RSpec.configure do |config|
  config.include FactoryBot::Syntax::Methods

  #... 略
end
```

`$ rspec`
第一次跑rspec時, SimpleCov會製作一包coverage/是他需要的素材, 建議ignore掉沒關係
``` gitignore .gitignore
coverage/
```
最後, 以後下`$ rails g xxxx yyyy`都會建立該rspec檔案

# Unit test心法

1. unit test 的原則為 AAA
  * Arrange: 安排 => 設定好 let, allow 等會用到的東西
  * Action: 執行 => 這點還沒參透, 總覺得執行不就已經寫在 method 裡面了嗎?
  * Assert: 結果 => 就是白話, it expect等
2. 由淺入深有 3 個 level, 會深深影響 coverage
  * method => 最單純的情況, 大多是運算或者是屬性
  * branch => 最簡單的 if else 有兩條路要走, 都要測到
  * condition => if a > b || b < c 等, 會有很多細微的排列組合
3. 優先只要測 public method 就好
4. 如果測試很難寫, 很有可能是程式碼寫得不好, 這邊是說真正在執行的程式碼, 不是 rspec 的

# 基本用法

我們先建立最簡單的 model User(我這邊用devise建立), 只有 email 和 age 兩個欄位
```ruby app/models/users.rb
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable, :trackable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable

  class << self
    def all_adults
      User.where(["age >= ?", 18])
    end
  end

  def username
    email.split('@').first
  end
end
```

model 建立好之後做 FactoryBot
```ruby spec/factories/users.rb
FactoryBot.define do
  factory :user do
    email { 'rails@gmail.com' }
    age { 10 }
    password { 'password' }
    password_confirmation { password }
  end
end
```

最後寫對於 User 這個 model 寫 unit test
```ruby spec/models/user_spec.rb
require 'rails_helper'

RSpec.describe User, type: :model do
  describe '.all_adults' do
    context 'when users age greater than 18' do
      subject { User.all_adults }
      let!(:user1) { create(:user, email: 'user1@gmail.com', age: 17) }
      let!(:user2) { create(:user, email: 'user2@gmail.com', age: 18) }
      let!(:user3) { create(:user, email: 'user3@gmail.com', age: 20) }

      it { is_expected.to eq([user2, user3]) }
    end
  end
  describe '#username' do
    context 'shold return username by email' do
      subject { user.username }
      let(:user) { create(:user, email: 'kurt@gmail.com') }

      it { is_expected.to eq('kurt') }
    end
  end
end
```

## unit test慣例1: describe

心法3提到基本上都先測 public method, 而 class 等級的為`.`, instance 等級的為 `#`
```ruby
describe '.class_public_methods'
```
```ruby
describe '#instance_public_methods'
```

## unit test慣例2: context

我們新增一個簡單的 instance method(先不論這判斷很多餘XD)
```ruby app/models/users.rb
def invalidated_age?
  if age < 0
    true
  else
    false
  end
end
```
剛剛心法2有提到三個等級method, branch, condition, 這邊比較單純的到 branch 而已, 通常`用 with, when 當開頭`
```ruby spec/models/user_spec.rb
describe '#invalidated_age?' do
  subject { user.invalidated_age? }
  let(:user) { create(:user, email: 'kurt@gmail.com', age: age) }

  context 'when user age greater than 0' do
    let(:age) { 10 }

    it { is_expected.to be_falsey }
  end

  context 'when user age greater than 0' do
    let(:age) { -10 }

    it { is_expected.to be_truthy }
  end
end
```

當然很多時候會有巢狀判斷, context 也一樣巢狀測試下去就行了

## unit test慣例3: mocking

unit test 專注於一個 method 測試一個東西得到一個結果, 所以 mocking 可以略過其他非重點流程 or 變數

假裝我有一個 class method 要找最出的變種人
```ruby app/models/users.rb
class << self
  def the_very_first_mutant
    mutants = all_mutants
    egypt_mutants = find_egypt_mutants(mutants)
    blue_skin_mutants = find_blue_skin_mutants(egypt_mutants)
    blue_skin_mutants.order(age: :desc).first
  end

  def all_mutants
    # some coding...
  end

  def find_egypt_mutants(mutants)
    # some coding...
  end

  def find_blue_skin_mutants(egypt_mutants)
    # some coding...
  end
end
```
這個 method 是用 orm 一步步找到最初變種人, 但找的過程不重要, 且很確定最後一個找的條件(method) find_blue_skin_mutants 一定是給我一個 AR array, 所以我直接在測試 mocking 如下即可:
```ruby spec/models/user_spec.rb
describe '.the_very_first_mutant' do
  subject { User.the_very_first_mutant }

  let!(:user1) { create(:user, email: 'user1@gmail.com', age: 5000) }
  let!(:user2) { create(:user, email: 'user2@gmail.com', age: 50) }

  before do
    allow(User).to receive(:find_blue_skin_mutants).and_return(User.all)
  end

  context 'when user is first blue skin mutant in egypt' do
    it do
      is_expected.to eq(user1)
    end
  end
end
```

# 檢查 coverage

最後在終端機下 `$ open coverage/index.html` 即可看到 test coverage 的頁面, 由 simpleCov 這個 gem 產生

# 後記

本身寫的還不夠多, 一開始想掌握 unit test 花了不少時間所以想記錄下來最基本的狀況, testing 肯定還有更多更複雜的狀況會發生要去判斷, 永遠要記住 unit test 單位測試顧名思義就是每次只測一件事情得到一個結果, 沒有一定或最漂亮的寫法, 但 unit test 不要太過複雜化

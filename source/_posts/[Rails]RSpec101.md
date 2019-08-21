---
title: '[Rails]RSpec101'
date: 2018-05-11 10:24:00
categories:
 - [Rails, RSpec]
tags:
 - Rails
 - RSpec
---
## 純粹ruby測試
---
安裝在本機
`$ gem install rspec`
會安裝5~6個東西

接著在資料夾裡下
`$ rspec --init`
就可以開始玩了

## Rails專案裡實作
---
``` ruby Gemfile
group :development, :test do
  gem 'byebug', platform: :mri
  gem 'rspec-rails', '~> 3.5.2'
  gem 'rails-controller-testing'
end
```
`$ bundle install`
`$ bin/rails generate rspec:install`
`$ rspec`
應該可以運作，terminal出現訊息如下：
```
No examples found.
Finished in 0.0.00028 seconds (files took 0.08784 seconds to load)
0 examples, 0 failures
```

就代表成功，可以參考Github上的專案[RSpec101](https://github.com/myohmy10420/RSpec101/tree/CRUD-test)的CRUD-test分支做出簡單的CRUD test。

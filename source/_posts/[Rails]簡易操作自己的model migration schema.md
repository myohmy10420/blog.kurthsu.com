---
title: '[Rails]簡易操作自己的model migration schema'
date: 2018-04-13 15:16:00
categories:
 - [Rails]
tags:
 - Rails
 - schema
 - migration
---
本篇只是嘗試著練習及把玩model migrate schema的新增修改以及刪除

參考文章
[Rails實戰聖經](https://ihower.tw/rails/)
[Ruby on Rails 指南手冊](https://guides.ruby.tw/rails3/index.html)

## 介紹
[model](https://ihower.tw/rails/activerecord.html)
可以建立表單相依性和建立商業邏輯等等的地方

[migration](https://ihower.tw/rails/migrations.html#sec1)
在Rails裡可以操作資料庫的檔案-搭配rake執行任務

[schema](https://guides.ruby.tw/rails3/migrations.html#6-1)
資料庫綱要-通常由migration執行任務去控制`千萬不要直接去改動它`

---
## 正常的建立model和資料庫表單
最終會得到：
1.新的model
2.新的migration
3.更新schema

通常一次性的建立model和要在資料庫建立table使用以下指令
`$ rails g model car`

![螢幕快照 2018-04-13 下午9.17.00.png](http://user-image.logdown.io/user/18810/blog/18323/post/7357751/sEDNWORPaDNQ35A20SQv_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202018-04-13%20%E4%B8%8B%E5%8D%889.17.00.png)

我們建立了
car 的 model
 `app/models/car.rb`
create car的migration
`db/migrate/xxxxxxxxxxxxx_create_cars.rb`

這時候我們會去更新db/migrate/xxxxxxxxxxxxx_create_cars.rb這隻檔案賦予他等等建立這個表單有什麼項目，最後執行`$ rake db:migrate`更新資料庫綱要schema.rb並且在資料庫建立這個表單

---
## 刪除model
`$ rails destroy model car`

---
## 修改資料庫表單
通常一個表單不會一次建立好，例如我們要去修改剛剛car的這個表單要如何實作？
例如想在表單內新增車的顏色欄位
`$ rails g migration add_car_color`
找到那隻檔案並修改
``` javascript
class AddColorToCar < ActiveRecord::Migration[5.0]
  def change
    add_column :car, :color, :string
  end
end
```
`注意這裡是單數`
最後下指令更新資料庫表單和綱要
`$ rake db:migrate`
其他方法可以參考[Rails實戰聖經](https://ihower.tw/rails/migrations.html#sec1)

最後可以去查看schema.rb是否更新了

---
## 刪除資料庫表單
一樣利用migration
`$ rails g migration drop_car`
找到那隻檔案並修改
``` javascript
class AddColorToCar < ActiveRecord::Migration[5.0]
  def change
    drop_table :cars
  end
end
```
`注意這裡是複數`
最後下指令更新資料庫表單和綱要
`$ rake db:migrate`
最後可以去查看schema.rb是否更新了

---

## Git的補充
狀況1:取消已追蹤的所有修改`$ git checkout .` 單隻檔案 `$ git check —<filename>`
狀況2:刪除未追蹤的所有檔案`$ git clean -f`
狀況3:取消不小心git add .的所有檔案`$ git reset`單隻檔案`$ git reset filename`

特殊狀況:
`如果已經migrate到資料庫裡面了，千萬不要以為還原檔案包括schema.rb就好了唷！`
我們還是要創造一隻migration把資料庫的表單做更新之後才可以把這些檔案還原。

舉例來說我創造了car表單並且migrate到資料庫裡面了，這時候即使我把所有檔案還原並且也把schema.rb還原了，我再次下rails g model car的時候他會抱錯說表單已經存在，這時候只能去`rails console`看看怎麼回事，然後一樣新增migration的任務去執行更新資料庫了！

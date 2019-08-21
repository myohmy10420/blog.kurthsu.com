---
title: '[Rails] FriendlyId 基本教學 操作'
date: 2018-04-17 18:24:00
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - FriendlyId
---
參考來源:
[FriendlyId](https://github.com/norman/friendly_id)
[FriendlyId完整文件](http://norman.github.io/friendly_id/file.Guide.html)

目的創造將這種網址
some_domain/products/1
變為
`some_domain/products/goods1`

這邊紀錄我每個步驟包括遇到的問題

## 步驟1: 安裝
---
``` javascript Gemfile
gem 'friendly_id', '~> 5.1.0' # Note: You MUST use 5.0.0 or greater for Rails 4.0+
```
`$ bundle install`
`$ rails generate friendly_id`
`$ rails db:migrate`


## 步驟2: 創立表單(如果已經創立過則跳過此步驟)
---
[FriendlyId完整文件](http://norman.github.io/friendly_id/file.Guide.html)他會教你創migration這邊用product當示範:
`$ rails g model product`

找到該migration
``` javascript xxx_create_products.rb
class CreateProductLists < ActiveRecord::Migration[5.0]
  def change
    create_table :products do |t|
      t.string :title
      t.string :slug
  end
end
```
官方補充敘述:
>Temp solution for Rails 5.1+ : Before running the migration, go into the generated migration file and specify the Rails version:
>class CreateFriendlyIdSlugs < ActiveRecord::Migration[5.1]
>如果Rails版本超過5.1以上請修改migration第一行

`$ rake db:migrate`


## 步驟3: 實作
---
找到該model
``` javascript app/models/products.rb
class User < ApplicationRecord
  extend FriendlyId
  friendly_id :title, use: :slugged
end
```
找到該controller
``` javascript products_controller.rb
class UserController < ApplicationController
  def show
    @product = Product.friendly.find(params[:id])
  end
end
```

`如果跳過步驟2的到這肯定報錯，因為就有表單並沒有slug這個欄位，緊接著看步驟4！`

## 步驟4: 新創表單並且更新所有表單(有操作到步驟2的則不用做步驟4)
---
`$ rails g migration add_slug_to_product`

找到該migration
``` javascript add_slug_to_product.rb
class AddSlugToProduct < ActiveRecord::Migration[5.0]
  def change
    add_column :products, :slug, :string, unique: true
  end
end
```
`$ rake db:migtate`
最後在終端機更新所有表單
`$ rails console`
`$ Product.find_each(&:save)`
`$ exit`

---
大功告成！

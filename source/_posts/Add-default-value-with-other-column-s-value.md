---
title: "[Rails]Add default value with other column's value"
date: 2019-12-28 10:48:53
categories:
 - [Rails, Migration]
tags:
 - Rails
 - Migration
---
## 新增欄位且預設值是其他欄位的值

考慮的點如下:
1. 希望一個migration即可完成
2. 希望可以一次更新所有資料不要造成資料庫負擔

我一開始想有沒有什麼神奇的 helper 可以直接使用, 後來發現我想太多了其實很簡單

關於第一點, 就直接把ruby code寫在migration檔就可以
關於第二點, 使用update_all就可以一次性的更新所有資料, 但要注意 `update_all 不跑驗證, 且也不更新updated_at的timestamp`, 不過既然是新增一個欄位然後加default值, 要驗證的需求比較少, update_all就真的足夠了

檔案實作如下:

``` ruby
class AddSellingTimeToProducts < ActiveRecord::Migration[5.2]
  def change
    add_column :products, :selling_time, :datetime

    Product.all.update_all("selling_time = created_at")
  end
end
```

參考 update_all SQL messages:
```
(188.5ms)  ALTER TABLE `products` ADD `selling_time` datetime
Product Update All (19.6ms)  UPDATE `products` SET selling_time = created_at
```

參考文章:
[stackoverflow](https://stackoverflow.com/questions/5224881/update-one-column-to-value-of-another-in-rails-migration/5225626)
[Rails API](https://apidock.com/rails/v4.0.2/ActiveRecord/Relation/update_all)

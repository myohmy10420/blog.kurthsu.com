---
title: '[Rails]update_attribute vs update_column'
date: 2018-08-08 04:12:00
categories:
 - [Rails]
tags:
 - Rails
 - update_attribute
 - update_column
---
目前比較常看到更新資料用的有`save`、`update`、`update_attribute`和`update_attributes`方法

`save`預設會跑驗證，想跳過可以下
```ruby
person.save(validates: false)
#或者直接
person.save(false)
```

`update`跟`update_attributes`其實做的事情一樣因為大部分的人會這樣寫
```ruby
@person = Person.last
@person.update(person_pareams)
#使用update

@person.update_attributes(person_pareams)
#或使用update_attributes

@user = User.update(@user.id, user_avatar_params)
#update另一個update_attributes沒有但不建議的用法

#只單純User.update(@user.id, user_avatar_params)似乎不會寫進資料庫
#可以參考這篇stack overflow
#https://stackoverflow.com/questions/27684038/rails-paperclip-update-vs-update-attributes
```

比較特別的是`update_attribute`，他是直接`跳過驗證強制更新單筆資料`，跟update_attributes雖然很像但用途蠻不一樣的
```ruby
person.update_attributes(age: 20)
#只能給一個參數
```

在維護上我會建議不使用update_attribute和update_attributes

> 第一:要更語意話不如單純使用save或update

> 第二:update_attribute跟update_attributes命名真的太像做的事情卻不一樣(update_attribute是直接跳過驗證更新資料庫的)

> 第三:`update_attribute`很有可能之後被淘汰了因為Rails團隊新增了`update_colum`去做更棒的命名區別

`update_colum`跟update_attribute做的事情一模一樣不須通過驗證，也只能傳單筆資料，更棒的是`update_colums`是可以傳多筆資料不需要驗證，至少他們做的事情是一樣了吧！

故真的要都不驗證的話不如寫
```ruby
@person.save(validates: false)
#至少大家都看得懂

@person.update_colums(person_attributes)
#都加上s的話直傳單筆多比都ok
```

參考文章
[iT邦技術文章(直接看update那邊)](https://ithelp.ithome.com.tw/articles/10188185)
[關於save的api](https://apidock.com/rails/ActiveRecord/Base/save)
[關於update的api](https://apidock.com/rails/ActiveRecord/Base/update/class)
[關於update_columns的api](https://apidock.com/rails/ActiveRecord/Persistence/update_columns)

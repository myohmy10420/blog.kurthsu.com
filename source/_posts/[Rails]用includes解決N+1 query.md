---
title: '[Rails]用includes解決N+1 query'
date: 2018-09-10 23:02:44
categories:
 - [Rails]
tags:
 - Rails
 - includes
 - N+1 query
---
N+1 query幾乎是Rails一定會碰到的問題了XD
剛好買的書有提到就來玩一下, 其實先以server log到底什麼時候hit sql, 以及hit了幾次就能解決

## 問題發生
先假設User有3個Post, 我們很常在controller裡面寫
```
def index
  @posts = Post.all
end
```
然後顯示這篇Post的User name會在view裡寫
```
<% @post.each do |post| %>
  <%= post.user.name %>
<% end %>
```
這時候查看server log會發現hit了sql 4次, 這個就是N+1 query, 如果今天User有1000筆post就會hit 1001次query, 就會影響效能
```
Post Load (0.2ms)  SELECT "posts".* FROM "posts"
User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
```

## 解決方案by includes
簡單的解決方法先把User includes近來給Post即可
```
def index
  @posts = Post.includes(:user).all
end
```
再來查看server log只hit 2次
```
Post Load (0.1ms)  SELECT "posts".* FROM "posts"
User Load (0.1ms)  SELECT "users".* FROM "users" WHERE "users"."id" = ?  [["id", 1]]
```

## 觀察hit時間
其實觀察hit的時間也蠻重要的, 如果寫下列程式碼
```
def index
  @posts = Post.all
  binding.pry
  Post.first
  binding.pry
  puts 'asd'
end
```
將會發現第一次binding.pry`直接撈Post.all並不會hit sql`, 而第二次binding.pry的`Post.first有hit sql`, 這似乎是rails lazy load做的事情, 如果撈資料還有可能串上更多orm撈資料的時候他並不會真的先衝進資料庫撈資料, 一直要到`確定這筆資料要被使用了或者後面不能再串orm了就會hit sql`, 所以也可以發現`第二次binding.pry的Post.all還是沒有hit sql`, 要一直到view開始each render這些post的時候才會去hit sql, 有這個資訊的話會更好去調sql上的效能

參考書籍
為你自己學Ruby on Rails2017.09初版

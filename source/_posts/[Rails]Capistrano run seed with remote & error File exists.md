---
title: '[Rails]Capistrano run seed with remote & error File exists'
date: 2018-05-06 16:12:00
categories:
 - [Rails, Gem]
 - [Capistrano]
tags:
 - Rails
 - Gem
 - Capistrano
 - seed
---
參考文件：
[Rails 部署 Capistrano 如何使用seed档](http://hukui-blog.logdown.com/posts/2017/05/10/rails-deploy-capistrano-seed)
[capistrano-rails-db](https://github.com/kentaroi/capistrano-rails-db)

## 使用
---
假設已經使用了Capistrano部署完，如沒有參考：
[[Rails] Capistrano 部署rails專案到 linode](http://myohmy10420-blog.logdown.com/posts/7442885)

再來開始使用套件
``` ruby Gemfile
gem 'capistrano',  '~> 3.1'
gem 'capistrano-rails', '~> 1.1'
gem 'capistrano-rails-db'
```

``` ruby Capfile
require 'capistrano/rails'
require 'capistrano/rails/db'
```
官方文件上是寫cap deploy:db:seed，我使用如下：
`$  cap production deploy:db:seed`
就可以了，其他指令應該依樣畫葫蘆就好。

## Error
---
我的seed檔有包含創造圖片，使用[paperclip](https://github.com/thoughtbot/paperclip)這個套件，因為之前設定deploy.rb檔錯誤發生error，敘述如下：

我把
``` ruby deploy.rb
append :linked_dirs, "log", "tmp/pids", "tmp/cache", "tmp/sockets", "public/system"
```
設定成
``` ruby deploy.rb
append :linked_dirs, "log", "tmp/pids", "tmp/cache", "tmp/sockets", "public/system/000/000"
```
並且`$ cap production deploy`過

造成run `$  cap production deploy:db:seed`的時候release出錯：
 01 Errno::EEXIST: File exists @ dir_s_mkdir - /home/deploy/movie_review/releases/20180506145631/public/system/movies/images/000/000

所以要手動到機台上面進去這個資料夾把/home/deploy/movie_review/releases/20180506145631/public/system/movies/images/000/
刪除即可

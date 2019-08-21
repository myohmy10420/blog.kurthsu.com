---
title: '[Rails]操作遠端的 rake 和 console'
date: 2018-05-09 13:27:00
categories:
 - [Rails]
tags:
 - Rails
 - rake
 - console
---
原本只是想利用[capistrano-rails-db](https://github.com/kentaroi/capistrano-rails-db) 這個套件輕鬆在本地端下指令就好，但rails5有新增一個保護機制，只要drop或者reset會被擋住，只要不要有動到就資料的動作其他其實也蠻正常的，像是cap deploy:db:seed之類的。

是什麼保護機制可以參考這篇：
[防止Production数据库被意外清空 | Rails 5](http://www.10tiao.com/html/568/201608/2650829521/1.html)

情況：
`我是用Capistrano部署在機台上的專案web server是Nginx`

## 如何執行rake
---
其實直接進到機台操作還是最簡單直接的，首先進到專案裡的current資料夾

如果要reset或drop db的話請先關閉web server：
`$ service nginx stop`

再來基本上都可以動rake db的指令了，例如reset：
`$ RAILS_ENV=production bin/rake db:reset DISABLE_DATABASE_ENVIRONMENT_CHECK=1`
DISABLE_DATABASE_ENVIRONMENT_CHECK就是rails5的保護機制，可以參考上面那篇。

都控制玩db之後重新開啟即可：
`$ service nginx start`

## 如何進入console
---
一樣到專案裡的current資料夾
`$ bundle exec rails c production`
就可以嘍！

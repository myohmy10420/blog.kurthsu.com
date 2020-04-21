---
title: '[Rails] 新增 rails 6 專案'
date: 2020-04-21 11:49:28
categories:
 - [Rails]
tags:
 - Rails
---

最近新增了一個專案玩玩, 直接使用 rails 6, 挺簡單沒有太多雷的, webpacker 變成 default 要安裝了

`$ ruby -v` ruby 如果 2.5.0 以下需要更新
`$ rvm install 2.6.3` 我是直接用 rvm 更新

`$ gem install rails` 會去抓 github 上最新版本的 rails
或者想指定版本
`$ gem install rails -v '6.0.0'`

啟動新專案
`$ rails new repo_name`
`$ cd repo_name`
`$ bundle install`
`$ rails webpacker:install`
`$ rails start`

應該就可以成功開啟 rails server 囉

參考文章
[Rails 6 released - What does that mean for you?](https://dev.to/chrisachard/rails-6-released-what-does-that-mean-for-you-3591)

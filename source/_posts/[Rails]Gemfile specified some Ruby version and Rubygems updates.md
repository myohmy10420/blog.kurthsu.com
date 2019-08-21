---
title: '[Rails]Gemfile specified some Ruby version and Rubygems updates'
date: 2018-04-24 13:35:00
categories:
 - [Rails]
tags:
 - Rails
 - Gemfile
---
與別人協同專案的時候常會遇到版本問題，在clone下來專案的時候有下`$ bundle install`可能會遇到：
Your Ruby version is 2.3.1, but your Gemfile specified 2.4.4

此例子是他想要ruby版本是 2.4.4但目前是 2.3.1

這時候利用rvm可以先做下列動作：
`$ rvm list` 可以查詢目前本地端有的版本

此時如果沒有指定的版本則下：
`$ rvm install ruby-x.x.x` x.x.x是想要安裝的版本

如果已經有或安裝好要使用則下：
`$ vm use ruby-x.x.x` 後面可以加--default來預設以後都用這個版本

再來下：
`$ bundle install` 他可能會說找不到指令(zsh: command not found: bundle)代表還沒安裝bundle(以前用rails new太順不知道這原來是一個gem XD)

如果沒有安裝的話就下：
`$ gem install bundler` 安裝bundler

這時候基本上可以bundle install成功了，但是下`$ rails s`開啟伺服器的時候又給以下訊息：
Warning: You're using Rubygems 2.0.14.1 with Spring. Upgrade to at least Rubygems 2.1.0 and run \`gem pristine --all\` for better startup performance.
Your Ruby version is 2.0.0, but your Gemfile specified 2.4.4

一開始想WTF我不是已經指定Ruby version是2.4.4了嗎？我就開始往Rubygems的問題去找，關鍵字是下update Rubygems，有提供很多種方法我是用最簡單的下：
`$ gem update`

更新了許久的時間終於專案的環境架設成功，可以開始開發！

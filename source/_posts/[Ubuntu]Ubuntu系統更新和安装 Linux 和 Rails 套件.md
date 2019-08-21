---
title: '[Ubuntu]Ubuntu系統更新和安装 Linux 和 Rails 套件'
date: 2018-04-27 13:03:00
categories:
 - [Ubuntu]
tags:
 - Ubuntu
---
## 更新和安装 Linux 套件
---
apt-get是 Ubuntu 内建的套件管理工具與 Mac 上的 homebrew雷同，在雲端伺服器上更新系統套件清單，然後升級。

`sudo apt-get update`
`sudo apt-get upgrade -f` - Do you want to continue? [Y/n]直接按enter

設定時區：
可以先看預設時區
`$ date`
應該跟自己的時間對不起來，如果想設定台灣時區：
`sudo dpkg-reconfigure tzdata`
選擇Asia > TaiPei後再次檢查
`$ date`
應該就會是剛剛的時間加八小時(UTF+8)了！

接這一行安裝Rails需要的東西：
`sudo apt-get install -y build-essential git-core bison openssl libreadline6-dev curl zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-0 libsqlite3-dev sqlite3 autoconf libc6-dev libpcre3-dev libcurl4-nss-dev libxml2-dev libxslt-dev imagemagick nodejs libffi-dev`
一樣Do you want to continue? [Y/n]直接按enter

小補充：
如果`$ apt-get update`卡在0% [Connecting to security.ubuntu.com (2001:67c:1360:8001::17)]可能是因為ipv6不穩，可以設定只使用apt-get 只使用 ipv4，編輯這個檔案：
``` txt /etc/sysctl.conf
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```
最後執行`$ sudo sysctl -p`再重新run`$ sudo apt-get update`

## 安装 Ruby
---
使用[Brightbox](https://www.brightbox.com/docs/ruby/ubuntu/)安裝Ruby套件：
`$ sudo apt-get install software-properties-common`
`$ sudo apt-add-repository ppa:brightbox/ruby-ng`
`$ sudo apt-get update`
`$ sudo apt-get install ruby2.4 ruby2.4-dev`
`$ ruby -v`
顯示ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-linux-gnu]代表成功

安裝 [Bundler](http://bundler.io/ gem)：
`$ sudo gem install bundler --no-ri --no-rdoc`
--no-ri --no-rdoc 参数的意思是不需要安装文档，可以节省安装时间

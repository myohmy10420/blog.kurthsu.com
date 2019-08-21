---
title: '[Devops]安裝數據庫和Nginx + Passenger 網站服務器'
date: 2018-04-29 04:37:00
categories:
 - [Devops]
tags:
 - Devops
 - Nginx
 - MySQL
 - PostgreSQL
 - Passenger
---
## 安裝數據庫
---
#### MySQL
`$ sudo apt-get install mysql-common mysql-client libmysqlclient-dev mysql-server`
過程中會要設定root密碼

進入mysql console：
`$ mysql -u root -p`

建立數據庫：
`$ CREATE DATABASE rails_recipes CHARACTER SET utf8mb4;`
`rails_recipes`名稱可以自己取不能有-這個符號
`$ exit`離開mysql console

#### PostgreSQL
`$ sudo apt-get install postgresql libpq-dev postgresql-contrib`

修改postgres這個帳號的密碼：
`sudo -u postgres psql`
`\password`
然後輸入密碼和確認密碼
`\q`離開

建立數據庫：
`sudo -u postgres createdb rails_recipes`
`rails_recipes`名稱可以自己取不能有-這個符號


## 安装 Nginx + Passenger 網站服務器
---
在本機開發的時候，我們使用 [puma](https://github.com/puma/puma) 這一套由 Ruby 寫的網站服務器，無論是靜態檔案(圖片/CSS/JS)或是會進到 Rails 處理的動態網頁，一律都是由 puma 來處理。

在正式production 環境中，我們會用更高效能的網站服務器來處理，其中[Nginx](http://nginx.org/)是目前最流行的網站服務器(用C語言開發的)，可以非常高效能地提供靜態檔案，效能是純Ruby 網站服務器的數十倍以上。因此像圖檔/CSS/JS等等靜態資源，都會由 Nginx 處理。至於 Rails 動態網頁的部分，我們會安裝 Passenger 這個 Nginx 的擴充模組來執行 Ruby 程序：Nginx 會把非靜態檔案的 HTTP Request 轉交給 [Passenger](https://www.phusionpassenger.com/) 來處理。

`$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7`
`$ sudo apt-get install -y apt-transport-https ca-certificates`
`$ sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger xenial main > /etc/apt/sources.list.d/passenger.list'`
`$ sudo apt-get update`
`$ sudo apt-get install -y nginx-extras passenger`

現在直接在遊覽器打自己主機的位置應該可以看到Welcome to nginx!頁面了

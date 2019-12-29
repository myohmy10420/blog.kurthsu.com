---
title: '[Rails] Capistrano 部署rails專案到 linode'
date: 2018-04-29 05:38:00
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - Capistrano
 - Linode
---
總的來說[Capistrano](http://capistranorb.com/)會做到下列事情：
1.抓取Github上的最新版本
2.自動bundle 安装套件、 跑數據庫 migration、 編譯 assets  編譯等等
3.在deploy新版本的時候舊有的版本仍然可以運行，使用者感受不大

開始之前linode上的主機要先裝好Rails環境和數據庫，可以參考下面先前的文章：
{% post_link '[Ubuntu]Ubuntu系統更新和安装 Linux 和 Rails 套件' '[Ubuntu]Ubuntu系統更新和安装 Linux 和 Rails 套件' %}
{% post_link '[Devops]安裝數據庫和Nginx + Passenger 網站服務器' '[Devops]安裝數據庫和Nginx + Passenger 網站服務器' %}

## 先在自己的專案進行設定
---
安裝數據庫類型的gem和capistrano
MySQL的數據庫的話安裝`mysql2`
PostgreSQL的數據庫的話安裝`pg`

修改Gemfile：
``` ruby Gemfile
gem 'mysql2'  # mysql2 和 pg 择一安装即可
gem 'pg'

group :development, :test do
  gem 'capistrano-rails'
  gem 'capistrano-passenger'

  gem 'rspec-rails'
  gem 'byebug', platform: :mri
end
```
`$ bundle install`

再來開始安裝和設定capistrano：
`$ cap install`
此時會自動產生檔案

把剛剛產生的檔案做一些設定：
``` javascript Capfile
  require "capistrano/scm/git"
  install_plugin Capistrano::SCM::Git

+ require 'capistrano/rails'
+ require 'capistrano/passenger'
```

``` ruby config/deploy.rb
+ sh "ssh-add"

  # config valid only for current version of Capistrano
  lock "3.8.1"

- set :application, "my_app_name"
+ set :application, "rails_recipes"   #  請用你自己的項目名稱

- set :repo_url, "git@example.com:me/my_repo.git"
+ set :repo_url, "git@github.com:xxxxxxxxxx/rails-recipes.git"    #  請用你自己項目的git位置

  # Default branch is :master
  # ask :branch, `git rev-parse --abbrev-ref HEAD`.chomp

  # Default deploy_to directory is /var/www/my_app_name
  # set :deploy_to, "/var/www/my_app_name"
+ set :deploy_to, "/home/deploy/rails-recipes"     #  這樣服務器上代碼的目錄位置，放在 deploy  帳號下。 請用你自己的項目名稱。

  # Default value for :format is :airbrussh.
  # set :format, :airbrussh

  # You can configure the Airbrussh format using :format_options.
  # These are the defaults.
  # set :format_options, command_output: true, log_file: "log/capistrano.log", color: :auto, truncate: :auto

  # Default value for :pty is false
  # set :pty, true

  # Default value for :linked_files is []
- # append :linked_files, "config/database.yml", "config/secrets.yml"
+ append :linked_files, "config/database.yml", "config/secrets.yml"

  # Default value for linked_dirs is []
- # append :linked_dirs, "log", "tmp/pids", "tmp/cache", "tmp/sockets", "public/system"
+ append :linked_dirs, "log", "tmp/pids", "tmp/cache", "tmp/sockets", "public/system"

+ set :passenger_restart_with_touch, true

  # Default value for default_env is {}
  # set :default_env, { path: "/opt/ruby/bin:$PATH" }

  # Default value for keep_releases is 5
- # set :keep_releases, 5
+ set :keep_releases, 5
```

修改 config/deploy/production.rb 設定要用哪一個 branch 放在linode伺服器上，以及伺服器的地址：
``` ruby config/deploy/production.rb
+ set :branch, "master"
- # server "example.com", user: "deploy", roles: %w{app db web}, my_property: :my_value
+ server "xxx.xxx.xxx.xx", user: "deploy", roles: %w{app db web}, my_property: :my_value
```

都設定完了之後可以開始嘗試著建立Capistrano 需要的架構目錄在linode伺服器上：
`$ cap production deploy:check`
會看到一個 ERROR  說伺服器上缺少一些檔案， 再來要到linode服務器上新增這些檔案


## 在linode伺服器上設定檔案
---
這裡我創造了一個使用者deploy專門deploy這篇文章的Rails 專案！

`$ touch /home/deploy/rails-recipes/shared/config/database.yml`
rails-recipes請修改自己的專案名稱

再來如果是`MySQL`數據庫：
``` ruby /home/deploy/rails-recipes/shared/config/database.yml
production:
  adapter: mysql2
  encoding: utf8mb4
  database: rails_recipes
  host: localhost
  username: root
  password: xxxxxxxxxx
```
再來如果是`PostgreSQL`數據庫：
``` ruby rails-recipes/shared/config/database.yml
production:
  adapter: postgresql
  pool: 25
  database: rails_recipes
  host: localhost
  username: postgres
  password: xxxxxxxxxx
```
password是數據庫的密碼

再來到`本地端的專案`拿取Capistrano deploy需要的key
`$ rake secret`

回到`linode伺服器`上面新增檔案：
`$ touch /home/deploy/rails-recipes/shared/config/secrets.yml`
``` ruby /home/deploy/rails-recipes/shared/config/secrets.yml
production:
  secret_key_base: keyxxxxxxxxxx
```
把剛剛在`本地端`拿取的key貼到secret_key_base裡面

這時候再一次在`本地端`用Capistrano check deploy 一次：
`$ cap production deploy:check`
就不會再噴ERROR了！

如果沒有跟ERROR在`本地端`用Capistrano執行deploy：
`cap production deploy`
第一次部署可能需要到數十分鐘，且可能因為網路或伺服器CPU等級比較差而失敗，可以再多跑幾遍即可。

成功了以後之後要更新版本的話，只要保持此專案的Github為最新版本即可，因為他是去抓Github專案的版本去doploy，一樣在`本地端`下`cap production deploy`即可。


小補充：
在 config/deploy.rb 的設定中，linked_files 和 linked_dirs 就是在配置部署過程中，需要將這些 shared 下的檔案和目錄，連結到新的 releases 目錄裡面去。

最後也可以參考如何設定Nginx設定
{% post_link '[Nginx]Nginx 設定' '[Nginx]Nginx 設定' %}

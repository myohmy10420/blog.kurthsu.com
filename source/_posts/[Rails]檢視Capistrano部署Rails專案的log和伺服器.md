---
title: '[Rails]檢視Capistrano部署Rails專案的log和伺服器'
date: 2018-04-29 08:56:00
categories:
 - [Rails, Gem]
tags:
 - Nginx
 - Gem
 - Capistrano
 - log
---
假設已經部署成功後進入該主機

## 專案log
---
檢查專案log
`$ cd ~/專案名稱/current`
`$ tail -n 500 log/production.log` 可以顯示500行
`$ tail -f log/production.log`可以不斷顯示下去

檢查Nginx的錯誤
`$ su root` 總之需要root權限
`$ vim /var/log/nginx/error.log`

## 系統狀態
---
`$ free -h`顯示內存
`$ df -h`顯示硬碟剩餘空間
`$ sudo passenger-status`顯示Passenger狀態
`$ sudo passenger-memory-stats`顯示Passenger內存狀態

`$ top`系統狀態
最上面的load average 三個數字分別代表最近1分鐘、5分鐘和15分鐘的系統平均負載。數字越小越好，表示系統不忙。如果你的服務器只有單顆 CPU，那這個數字應該要小於1。兩顆CPU則要小於2。

`$ ps ax`有點像打開系統管理員可以看到正在執行的程式
`$ sudo kill -9 <PID>`可以強制刪除該程序，這是最後手段

## Nginx重啟指令
---
默認安裝完以及開機後，就會啟動 Nginx。如果有修改 Nginx 設定檔，需要重開 Nginx。
`$ sudo service nginx start`啟動
`$ sudo service nginx stop`停止
`$ sudo service nginx restart`重啟

如果只是要重開 Rails，可以不重開 Nginx。在你的 Rails 目錄下(例如 /home/deploy/rails-recipes/current 這個目錄)執行 touch tmp/restart.txt 即可，這樣 Passenger 就會知道要重新加載 Rails，而不需要重開 Nginx。

## 進 rails console
---
到部署專案的使用者帳號
`$ cd ~/專案名稱/current`
`$ bundle exec rails c production`

## 如何在遠端跑 rake?
---
例如想在服務器上執行 rake db:seed：
到部署專案的使用者帳號
`$ cd ~/專案名稱/current`
`$  RAILS_ENV=production bundle exec rake db:seed`

## 整理 Log 檔案
---
Linux 內建有 logrotate 工具，可以定期清空和壓縮 Log 檔案。如果你不整理的話，Rails 的 production.log 會不斷成長到撐爆硬盤空間。

用 root 權限新增 /etc/logrotate.d/rails 檔案，內容如下：
``` javascript /etc/logrotate.d/rails
/home/deploy/專案名稱/shared/log/*.log {
  monthly
  dateext
  missingok
  rotate 65535
  notifempty
  copytruncate
}
```

`daily` 表示每天整理，也可以改成 weekly 或 monthly
`dateext` 表示檔案補上 rotate 的日期
`missingok` 表示如果找不到 log 檔也沒關係
`rotate` 表示保留65535份，建議如果硬盤空間夠的話，就不要砍log檔了，以供未來備查
`compress` 表示壓縮起來，默認用 gzip
`delaycompress` 表示延後壓縮直到下一次 rotate
`notifempty` 表示如果 log 檔是空的，就不 rotate
`copytruncate` 先複製 log 檔的內容後，在清空的作法，因為有些程式一定 log 在本來的檔名，例如 rails。另一種方法是 create。

如果是daily可以等明天或者是執行：
`$ sudo /usr/sbin/logrotate -f /etc/logrotate.conf`
應該可以看到rails 項目下的 log/production.log 被依日期拆開了。

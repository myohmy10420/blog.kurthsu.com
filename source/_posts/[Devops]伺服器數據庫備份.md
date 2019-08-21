---
title: '[Devops]伺服器數據庫備份'
date: 2018-04-29 10:26:00
categories:
 - [Devops]
tags:
 - Devops
 - Backup
---
通常我們要備份的東西有三個：
`code` 把專案上傳在github就已經很夠
`數據庫`這是最重要的需要匯出
`使用者上傳的檔案`如果專案有上傳檔案的功能就需要

## MySQL手動備份
---
如果有使用者上傳的檔案請先放到下面這個資料夾：
`/home/deploy/rails-recipes/shared/public/system`

再來把MySQL數據庫匯出：
`$ mysqldump -u root -p 專案名稱 > 檔案名稱.sql`
參數 `-u root` 表示用數據庫的 root 帳號、`-p` 表示要輸入密碼、匯出專案數據庫到`.sql`這個檔案

把剛剛匯出的檔案gzip壓縮：
`$ gzip 檔案名稱.sql`

#### 完成MySQL數據庫備份檔案，這隻檔案就可以備份在其他地方！

再來把備份的檔案匯入的話先解壓縮：
`$ gunzip 檔案名稱.sql`

匯入MySQL數據庫：
`$ mysql -u root -p 專案名稱 < 檔案名稱.sql`

## PostreSQL手動備份
---
如果有使用者上傳的檔案請先放到下面這個資料夾：
`/home/deploy/rails-recipes/shared/public/system`

再來把PostreSQL數據庫匯出：
`$ pg_dump -W -U postgres -h localhost 專案名稱 > 檔案名稱.sql`

把剛剛匯出的檔案gzip壓縮：
`$ gzip 檔案名稱.sql`

#### 完成PostreSQL數據庫備份檔案，這隻檔案就可以備份在其他地方！

再來把備份的檔案匯入的話先解壓縮：
`$ gunzip 檔案名稱.sql`

匯入MySQL數據庫：
`$ psql -W -U postgres -h localhost 專案名稱 < 檔案名稱.sql`

## 自動備份
---
使用https://github.com/backup/backup 這個Ruby 工具可以幫助我們設定好自動備份，可以參考https://gist.github.com/ihower/5a28624f9420fb9a7c49 這會備份整個mysql 數據庫，打包壓縮整個/ srv 目錄，上傳到指定的AWS S3 bucket，最後寄送email 通知完成。

在服務器上執行 crontab -e 可以編輯例行性工作排程(crontab)，以下是一個每日凌晨 4:30 自動執行的範例：
`$ 30 4 * * * /bin/bash -l -c '/usr/local/bin/backup perform -t my_backup -c /home/ihower/Backup/config.rb'`

---
title: '[Devops]Simple ways to clean sever space'
date: 2020-01-19 16:06:59
categories:
 - [Devops]
tags:
 - Devops
---
最近自己做的一個小網站放在免費的EC2磁碟空間只有7.7G, 加上因為虛擬記憶體不夠用又分給swap 1G, 基本上網站run起來後可用磁碟空間只剩不到1G, 要怎麼善用每個空間變成一個很有趣的問題


## 基本ubuntu指令
1. 查看硬碟空間: `$ df -h`
> /dev/xvda1      7.7G  7.3G  484M  94% /
代表總共7.7G的硬碟我已經用掉了94%

2. 找出大於指定大小的檔案: `$ sudo find / -type f -size +80M -exec ls -lh {} \;`
> +80M是大於80M以上的檔案, 可以自己改動, 我是從80M開始找

3. 顯示該資料夾下所有檔案的大小: `$ ls -lh`


## 專案log處理

在ubuntu的機台上就可以對指定的log做一些設定
首先先找到專案production.log位置
`$ cd your_app/current/log`
顯示路徑並且複製起來
`$ pwd`
再來切換到root比較方便做事
`$ sudo su -`
找到設定檔
`vim /etc/logrotate.conf`
拉到最下面新增以下
``` logrotate.conf
/home/user/your_app/current/log/production.log {
  daily
  missingok
  rotate 7
  compress
  delaycompress
  notifempty
  copytruncate
}
```
- daily –  每天都執行Rotate, 也可以設定每個禮拜(weekly)或者每個月 (monthly)
- missingok – 找不到log就忽略沒關係
- rotate 7 – 設定留下幾天的Rotate, 這邊是7天
- compress – 使用GZip壓縮Rotate的檔案
- delaycompress – Rotate the file one day, then compress it the next day, 這樣才不會干擾Rails server
- notifempty – 如果logs是空的就不執行Rotate
- copytruncate – 新增完壓縮檔後就把原本的logs檔案內容清空而不是刪除, 這樣也可以確保原本寫入的log檔案一直存在才不會出錯, 如果沒有設定這個每次Rotate完都要重啟rails server

再來可以等隔天或直接執行`$ /usr/sbin/logrotate -f /etc/logrotate.conf`然後到專案current/log底下查看結果

參考文章:
[Rotating Rails Production Logs with LogRotate](https://gorails.com/guides/rotating-rails-production-logs-with-logrotate)

我有另外找到可以防止test or deveopment log太肥的做法, 但似乎不適用於production:
[Limit the size of your Rails test and development logs](https://stormconsultancy.co.uk/blog/techtips/limit-the-size-of-your-rails-test-and-development-logs/)

## 清理套件管理APT沒用到的東西
在做之前可以下一下`$ df -h`查看, 然後清理完後再下`$ df -h`做比對
`$ sudo apt-get autoremove`
`$ sudo apt-get autoclean`
`$ sudo apt-get clean`

- clean: clean clears out the local repository of retrieved package files. It removes everything but the lock file from /var/cache/apt/archives/ and /var/cache/apt/archives/partial/. When APT is used as a dselect(1) method, clean is run automatically. Those who do not use dselect will likely want to run apt-get clean from time to time to free up disk space.

- autoclean: Like clean, autoclean clears out the local repository of retrieved package files. The difference is that it only removes package files that can no longer be downloaded, and are largely useless. This allows a cache to be maintained over a long period without it growing out of control. The configuration option APT::Clean-Installed will prevent installed packages from being erased if it is set to off.

- autoremove: is used to remove packages that were automatically installed to satisfy dependencies for some package and that are no more needed.

直接把參考文章的說明貼上

參考文章:
[https://askubuntu.com/questions/3167/what-is-difference-between-the-options-autoclean-autoremove-and-clean](https://askubuntu.com/questions/3167/what-is-difference-between-the-options-autoclean-autoremove-and-clean)

## 清理APT cache

先查看cache檔案大小
`sudo du -sh /var/cache/apt `
延續上面的指令
- sudo apt-get autoclean - 清理過期用不到的package cache
- sudo apt-get clean - 清理全部

## 清理 journal logs

Linux系統也會紀錄機台上發生了什麼事情, 可以參考[various services in Ubuntu](https://itsfoss.com/start-stop-restart-services-linux/)

如果真的不需要的話可先下指令查看log檔案多大
`$ journalctl --disk-usage`
或者清除老舊於指定天數的log, 例如3天
`$ sudo journalctl --vacuum-time=3d`

## 清理舊版本的Snap applications

這個倒是佔了我不少空間, 先下以下指令查看佔用多少空間
`$ du -h /var/lib/snapd/snaps`
/var/lib/snapd/snaps/partial 沒什麼關係
主要是/var/lib/snapd/snaps蠻大空間的

我們可以找個地方寫個shell檔案直接清理
`$ vim snaps_cleaner`
寫入以下代碼
```script snaps_cleaner
#!/bin/bash
# Removes old revisions of snaps
# CLOSE ALL SNAPS BEFORE RUNNING THIS
set -eu
snap list --all | awk '/disabled/{print $1, $3}' |
    while read snapname revision; do
        snap remove "$snapname" --revision="$revision"
    done
```
`$ wq`存檔後離開
`$ ./snaps_cleaner`直接執行它
`$ du -h /var/lib/snapd/snaps`在查看一次是否所佔空間有減少了

## 心得

以上幾個方法簡單又可以直接清空間, 發現cache檔和log檔真的是空間殺手呀!
7.7G的硬碟空間真的是要斤斤計較, 很多套件產生cache的機制真的沒有去研究過, 也深怕刪除了會影響機台什麼, 不過以上方法應該是最簡單又最沒有影響的手法了

## 統整參考文章
[7 Simple Ways To Free Up Space On Ubuntu and Linux Mint](https://itsfoss.com/free-up-space-ubuntu-linux/)
[Rotating Rails Production Logs with LogRotate](https://gorails.com/guides/rotating-rails-production-logs-with-logrotate)
[Limit the size of your Rails test and development logs](https://stormconsultancy.co.uk/blog/techtips/limit-the-size-of-your-rails-test-and-development-logs/)
[https://askubuntu.com/questions/3167/what-is-difference-between-the-options-autoclean-autoremove-and-clean](https://askubuntu.com/questions/3167/what-is-difference-between-the-options-autoclean-autoremove-and-clean)

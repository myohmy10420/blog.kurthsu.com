---
title: '[Devops]letsencrypt申請https (Nginx in Ubuntu 16.04)'
date: 2018-05-01 05:43:00
categories:
 - [Devops]
tags:
 - Devops
 - letsencrypt
 - SSL
 - Nginx
 - Ubuntu
---
使用前建議：
安裝並設定好Nginx和管理好DNS，可以參考這篇文章：{% post_link '[Devops]管理DNS並設定Nginx做出漂亮的網址' '[Devops]管理DNS並設定Nginx做出漂亮的網址' %}

---
[letsencrypt](https://letsencrypt.org/)可以直接申請免費的憑證，全程只要在自己的主機上下command就好。

到首頁GETTING STARTED之後他會建議使用Shell的人使用[Certbot](https://certbot.eff.org/)去遵循安裝步驟，很人性化的直接給你選使用什麼樣的web server在什麼樣的系統主機，我在這邊是使用`Nginx` on `Ubuntu 16.04(xenial)`

進入到自己的root，如果不是的話以下的command line前面都要加上sudo獲得root權限。

首先先使用apt安裝certbot的套件：
`$ apt-get update`
`$ apt-get install software-properties-common`
`$ add-apt-repository ppa:certbot/certbot`
`$ apt-get update`
`$ apt-get install python-certbot-nginx` 下這行之後他會建議安裝一些python的東西，回答Y給他安裝吧！

再來要設定在nginx上面：
`certbot --nginx`
這行自動設定在nginx的config檔上面，如果不喜歡他擅自更動檔案的話可以下：
`certbot --nginx certonly`

再來他會問一些問題：
1.Enter email address
輸入自己的email

2.會給網址請你去看條款並問是否同意
Y

3.想要哪個網域有https認證
他會列出Nginx你之前設定過的網域，輸入數字代碼即可

4.是否要讓訪問這個一定要有https認證，取消http
這邊我是選2

再來會給你一些重要資訊，譬如說私鑰它放在哪個資料夾，極度建議去備份。

再來打開自己的網頁，應該就成功嘍！

---
最後這個免費憑證是90天，會建議60天以後上去查看或更新，輸入以下指令：
`certbot renew --dry-run`
應該就可以更新嘍！

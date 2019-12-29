---
title: '[Devops]管理DNS並設定Nginx做出漂亮的網址'
date: 2018-04-30 07:15:00
categories:
 - [Devops]
tags:
 - Devops
---
### 所需：
購買一個網域，網路上例如linode這種平台都會提供賣網域的服務，我則是在[goDaddy](https://tw.godaddy.com/?ci=)買的。

什麼是[Nginx](https://www.nginx.com/)？
Nginx是一種web server，普遍有名的還有Apache等等，這篇紀錄如何用Nginx管理DNS(Domain name system)，就是當這個主機接收到什麼domain時會開啟什麼頁面。

可以想像web server是一個主機門口的遊覽器接待，只要是使用遊覽器訪問(request)這個主機一定要先通過這個接待的帶領，他最重要的工作如下：
1.拿取http(s)協定
2.帶領客戶端找到他要的東溪

## 管理DNS
---
先購買一個網域，例如購買了 test.com這個網域，最容易混淆也最常用的類型是A和CNAME。

簡單的來說：
A類型是引導到`主機IP`位置。
CNAME類型是引導到`別的網域`，雖然也可以直接打IP但很不建議這麼做。
參考文章：
[Differences between the A, CNAME, ALIAS and URL records](https://support.dnsimple.com/articles/differences-between-a-cname-alias-url/)

在GoDaddy進入到DNS 管理管理頁面，左上角可以看到自己網域的名稱，例如test.com。
如果新增或編輯A和CNAME都需要輸入名稱和值。

`名稱`是subdomain，一個網域可以創造很多個subdomain
`值`是真正要導向的IP或別的網域

subdomain又是什麼？
他可以利用同一個網域做出辨識度較高的網址

例如我有三個主機：
第一個主機是服飾店--------IP是111.111.111.11
第二個主機是動物用品店---IP是222.222.222.22
第三個主機是部落格--------IP是333.333.333.33

此時我可以新增三個A類型

名稱           | 值             |輸出Domain
--------------|:--------------:|------------------------
store.clothes | 111.111.111.11 | store.clothes.test.com
store.animal  | 222.222.222.22 | store.animal.test.com
blog          | 333.333.333.33 | blog.test.com

當然為了一個專案開一台主機實在是太浪費了，我們可以只在一台主機上利用Nginx導向不同的專案，後面會再提到。

如果是要導去別的域名建議使用CNAME，例如我在別人做的部落格註冊了自己的部落格：

名稱           | 值             |輸出Domain
--------------|:--------------:|------------------------
blog          | awsomeblog     | blog.test.com

但導過去之後網址還是會依別人的平台為主，但至少在收尋引擎上收尋test blog會比較容易找到自己的blog！
再傳給別人blog網址的時候也比較漂亮。

小小trick，如果再GoDaddy管理DNS假設在A類型名稱新增一個@：

名稱           | 值             |輸出Domain
--------------|:--------------:|------------------------
@             | 111.111.111.11 | test.com

就可以在CNAME這樣使用：

名稱           | 值             |輸出Domain
--------------|:--------------:|------------------------
store         | @              | store.test.com

但當然不建議這樣使用啦～

## 設定Nginx
---
如何安裝請參考 {% post_link '[Devops]安裝數據庫和Nginx + Passenger 網站服務器' '[Devops]安裝數據庫和Nginx + Passenger 網站服務器' %}

可以觀察`/etc/nginx/nginx.conf`檔案，可以在http底下建立server物件，指定什麼樣的domain訪問這個主機會開啟什麼樣的專案出來，就如同主機入口的招待一樣。

不過通常會習慣建立一個資料夾去處理各個server然後引入到`nginx.conf`這個檔案裡面去，他也會有一個`sites-enabled`的資料夾推薦放置在這。

假如沒有的話就自行建立(資料夾名稱sites-enabled可以自訂)：
`$ mkdir /etc/nginx/sites-enabled`

然後檢查nginx.conf檔案有沒有include進來：
``` javascript /etc/nginx/nginx.conf
…(略)
http {
  …(略)
  include /etc/nginx/sites-enabled/*;
}
...(略)
```

到自己的主機上面使用root，新增或修改`/etc/nginx/sites-enabled/rails-recipes.conf`
rails-recipes為當初自己設定的名稱：

``` javascript rails-recipes.conf
server {
  listen 80;
  server_name store.test.com animal.store.test.com;   # 用你自己的服务器 IP 位置可以多個用空格分開就好

  root /home/deploy/jdstore/current/public;  # 用你自己的项目名称位置

  passenger_enabled on;

  passenger_min_instances 1;

  location ~ ^/assets/ {
    expires 1y;
    add_header Cache-Control public;
    add_header ETag "";
    break;
  }
}
```

`listen`就是port
`server_name`就是剛剛管理DNS創立的domain，可以多個用空格分開就好！
`root`就是專案的資料夾

所以像剛剛管理DNS提到的一個主機放多個專案就是在 /etc/nginx/sites-enabled這個資料夾裡面建立，例如：
store.clothes.conf
store.animal.conf
blog.conf

最後都設定好的話下nginx指令跑更新：
`$ sudo service nginx restart`
沒報錯的話他不會有反應，有的話就去檢查設定再下一次這行指定。

如果設定好之後通常就可以使用自己創立的domain訪問看看，但`創造domian可能會等至多48小時`，以前比較常發生這種事情，不過現在通常是不到一分鐘就好了。

參考文章：
[Ruby on Rails 實戰聖經](https://ihower.tw/rails/deployment.html)
[Nginx 多網站配置](https://www.4rbj4.com/54)

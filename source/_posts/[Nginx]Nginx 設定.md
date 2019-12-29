---
title: '[Nginx]Nginx 設定'
date: 2018-04-29 07:44:00
categories:
 - [Nginx]
tags:
 - Nginx
---
假設已經利用Capistrano部署了一個Rails專案到雲端伺服器上，請參考文章：{% post_link '[Rails] Capistrano 部署rails專案到 linode' '[Rails] Capistrano 部署rails專案到 linode' %}

在`伺服器上`用`root權限`編輯/etc/nginx/nginx.conf：
``` javascript /etc/nginx/nginx.conf
  # 让 Nginx 可以读到环境变量 PATH，Rails 需要这一行才能调用到 nodejs 来编译静态档案
+ env PATH;

  user www-data;
  worker_processes auto;
  pid /run/nginx.pid;

  events {
    worker_connections 768;
    # multi_accept on;
  }

  http {

    # 关闭 Passenger 和 Nginx 在 HTTP Response Header 的版本资讯，减少资讯洩漏
+   passenger_show_version_in_header off;
+   server_tokens       off;

    # 设定档案上传可以到100mb，默认只有1Mb超小气的，上传一张图片就爆了
+   client_max_body_size 100m;

    gzip on;
    gzip_disable "msie6";

       # 最佳化 gzip 压缩
+   gzip_comp_level    5;
+   gzip_min_length    256;
+   gzip_proxied       any;
+   gzip_vary          on;
+   gzip_types application/atom+xml application/javascript application/x-javascript application/json application/rss+xml application/vnd.ms-fontobject application/x-font-ttf application/x-web-app-manifest+json application/xhtml+xml application/xml font/opentype image/svg+xml image/x-icon text/css text/xml text/plain text/javascript text/x-component;

    # 打开 passenger 模组
-   # include /etc/nginx/passenger.conf;
+   include /etc/nginx/passenger.conf;

    # 下略
```

在`伺服器上`用`root權限`編輯/etc/nginx/sites-enabled/rails-recipes.conf  這個檔案(檔名可以自訂無所謂)：
`$ /etc/nginx/sites-enabled/rails-recipes.conf`
``` javascript /etc/nginx/sites-enabled/rails-recipes.conf
server {
  listen 80;
  server_name xxx.xxx.xxx.xx;   # 用你自己的服务器 IP 位置

  root /home/deploy/rails-recipes/current/public;  # 用你自己的项目名称位置

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

設定完之後：
`sudo service nginx restart`
就成功了！

小補充：
如果 Nginx 設定檔格式不對，例如結尾少了分號 ;，那麼 sudo service nginx restart 會失敗，Nginx 服務器就死掉了。這時候請回頭修好設定檔，然後執行 sudo service nginx start 就會啟動 Nginx 了。如果無法啟動請檢查 /etc/nginx/nginx.conf 和 /etc/nginx/sites-enabled/rails-recipes.conf。

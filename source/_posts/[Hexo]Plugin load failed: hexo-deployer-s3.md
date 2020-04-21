---
title: '[Hexo]Plugin load failed: hexo-deployer-s3'
date: 2020-04-21 15:29:40
categories:
 - [Hexo]
tags:
 - Hexo
---

今天照常下 `$ hexo g -d` 準備 deploy, 終端機錯誤訊息如下:

```
ERROR Plugin load failed: hexo-deployer-s3
.(略)
.(略)
.(略)
.(略)
ERROR Deployer not found: s3
```

不知道為何 Plugin 一直 load 失敗
重新 yarn install, 整個 node_module 砍掉重安裝都一樣
查了一下都沒人遇到類似情況
後來降 node 版本竟然就解決了XD
這篇文章單純記錄一下狀況

`$ node -v`
v12.14.0 失敗
v8.11.2 正常

P.S. 我用 nvm

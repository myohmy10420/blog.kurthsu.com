---
title: '[AWS]解決svg圖片在S3無法顯示變成下載問題(by Asset Sync)'
date: 2019-08-25 22:23:04
categories:
 - [AWS, S3]
 - [Rails, Gem]
tags:
 - AWS
 - S3
 - svg
 - Rails
 - Gem
 - Asset Sync
---

## 環境
 - rails 4.2.9
 - asset_sync 2.0.0

## 遇到問題
利用asset_sync這個Gem上傳svg圖片, 不知為何這次的svg圖片顯示不出來, 且複製到新頁面自動變成下載(正常是開啟圖片)

## 快速解決方案
上S3找尋此檔案的`content-type `, 點選該檔案後在properties > metadata底下找尋, 應該要為`image/svg+xml`才是正確顯示圖片

## 修正Asset Sync設定
修正該檔案`config/initializers/mime_types.rb`, 加上一行程式碼即可:
```
Mime::Type.register "image/svg+xml", :svg
```

參考文章:
[Asset Sync的issue#323](https://github.com/AssetSync/asset_sync/issues/323)

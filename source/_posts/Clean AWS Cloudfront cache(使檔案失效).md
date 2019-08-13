---
title: Clean AWS Cloudfront cache(清除快取)
date: 2019-08-13 21:20:10
categories:
 - [AWS, Cloudfront]
 - [AWS, S3]
tags:
 - AWS
 - S3
---

上一篇教學文章{% post_link 利用Cloudfront讓Hexo部署到S3 '利用Cloudfront讓Hexo部署到S3' %}完成後, 只要改變文章再次 deploy 上去因為 Cloudfront 的 cache 特性並不會立即更新, 甚至連首頁的 index.html 頁面也沒有新的文章(但輸入正確網址其實是有的), 本篇記錄一下以下事情：
>1. 快速清 cache
>2. 找到正確的中文檔案路徑

## 快速清 cache
其實 AWS Cloudfront 已經把它做得非常簡單, 參考[官方文件](https://docs.aws.amazon.com/zh_tw/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html#invalidating-objects-console)說明是讓 cache 在 Cloudfront 的檔案立即失效, 首先直接到該 Cloudfront 的 Invalidations tag 介面, 然後點選 Create invalidation
{% asset_img cloudfront_invalidations_tag.png %}

先嘗試著清除首頁, 也就是在 Object paths 內輸入`/index.html`
{% asset_img create_invalidation.png %}
成功後就會看到他在跑, 單隻檔案的話大概一分鐘內就會跑完狀態轉為 Completed

這邊可以支援一次刪除多個檔案或選取多個檔案, 其實就跟 .gitignore 一樣的規則啦, 也可以參考[官方文件說明的失效路徑](https://docs.aws.amazon.com/zh_tw/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html#invalidation-specifying-objects-paths)

## 找到正確的檔案路徑
我們無法直接複製有中文的 Object paths, 例如此篇文章的 URL 為:
`/2019/08/13/Clean%20AWS%20Cloudfront%20cache(使檔案失效)/`
這是一個資料夾目錄所以他真正會去找的檔案為:
`/2019/08/13/Clean%20AWS%20Cloudfront%20cache(使檔案失效)/index.html`
但這也是錯的, 因為只要有中文字就是非法字元, 所以我們到 S3 Bucket 循著這個路徑一直找到該 index.html 檔案點選進去, 在 Overview 的畫面拉到最底下有個 `Object URL`, 除了 domain 之外都複製起來, 這個才是該檔案可以給 Invalidations 的合法路徑

## 注意
路徑一個月只免費一千條(雖然我是覺得一個簡單的 blog 來說超夠用了), 詳情請參考[官方文件支付檔案失效的費用](https://docs.aws.amazon.com/zh_tw/AmazonCloudFront/latest/DeveloperGuide/Invalidation.html#PayingForInvalidation)

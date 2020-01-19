---
title: '[Hexo]利用Cloudfront讓Hexo部署到S3'
date: 2019-08-11 10:32:21
categories:
 - [Hexo]
 - [AWS, S3]
tags:
 - Hexo
 - AWS
 - S3
---
終於把部落格架好了, 為了有DNS和Https花了不少時間看AWS文件, 結果朋友一篇文章就搞定了, 還跟AWS的教學完全不一樣XD

我認為Hexo部署到S3好處有幾點：
1. Hexo輕巧簡單(一鍵產生靜態檔案)
2. Hexo支持Markdown, 許多完善主題等玩家開發的功能
3. Hexo以後部署都很簡單
4. 圖片就都存到S3沒問題
5. Cloudfront支持https
6. Cloudfront又穩又快

## 事前準備
> 有一個Hexo專案
> 有一個AWS帳號
> 有一個DNS

## 先把Hexo部署到S3
先去[S3](https://s3.console.aws.amazon.com/s3/home?region=ap-northeast-1)建立Bucket, Bucket name可以隨便, Region 選 Tokyo比較快, 其他設定都不用動直接創立
{% asset_img create_bucket.png %}
點入剛剛創立的bucket, 點選 Properties > Static website hosting
{% asset_img bucket_properties.png %}
把它設定為Use this bucket to host a website, 再設定預設檔案之後存檔即可
{% asset_img bucket_to_host_website.png %}
再來點選 Properties > Permissions > Bucket Policy
可以看到 Policy 欄位是需要輸入程式碼的, 先記下Bucket policy editor ARN (我的是 arn:aws:s3:::kurt-blog), 然後點選最下面的 Policy generator 去產生程式碼
{% asset_img empty_bucket_policy.png %}
輸入以下資訊, 比較注意的是 Amazon Resource Name (ARN) 請輸入剛剛的 `ARN/*`, 輸入完後點選 Add statement
{% asset_img policy_generator1.png %}
出現了以下東西直接點選 Generate Policy, 就會跳出程式碼, 全部複製到 剛剛 S3 Policy 欄位儲存即可
{% asset_img policy_generator2.png %}
最後把 Block all public access 關閉, 仔細看的話他是可以選擇擋住哪些 ACLs 和 Policy, 這邊先不多追加研討, 如果全打開繼連剛剛設定的 Bucket policy 都會擋掉而失效了
{% asset_img s3_block_public_access.png %}
Hexo安裝[hexo-deployer-aws-s3](https://github.com/kei-ito/hexo-deployer-aws-s3)這個套件，在config.yml底下輸入：
``` config.yml
# Deployment
deploy:
  type: s3
  bucket: 剛剛創立的Bucket name
  aws_key: your_aws_key
  aws_secret: your_aws_secret
  region: ap-northeast-1
  headers: { CacheControl: 'max-age=604800, public' }
  delete_removed: true
```
aws_key, aws_secret請去[AWS security_credentials](https://console.aws.amazon.com/iam/home?region=ap-northeast-1#/security_credentials)上，點選Access key並且create一個
`注意: root key 產生後會給你下載, 請妥善保管不外流, 這是最大權限的 root key, 別人取得後可以在自己的 AWS 做所有事情, 且AWS也不會再給此組 key`

最後試著產生靜態檔案並且部署看看
```
$ hexo g -d
```
如果成功的話 Bucket 裡面會有檔案

## Coundfront + SSL 憑證
到[AWS Cloudfront](https://console.aws.amazon.com/cloudfront/home?region=ap-northeast-1)點選 Create Distribution > Web 的 Get Started
{% asset_img create_cloudfront.png %}
先拉到下面在ACM建立SSL
{% asset_img create_acm_ssl.png %}
輸入`*.yourdomain`, *以後就可以吃到所有子網域, 然後點選Next
{% asset_img import_domain.png %}
建議點選 E-mail 驗證比較快
{% asset_img acm_validation_type.png %}
`注意: 他是會發到自己申請的網域平台的信箱, 而不是此 AWS 帳號的信箱喔!!`
像我在 [Goddaddy](https://tw.godaddy.com/offers/domains/godaddy-b?isc=gofktw06&countryview=1&currencyType=TWD&gclid=CjwKCAjw1rnqBRAAEiwAr29IIwvCLrfpGBPzNt1lx5KMohPDAyaYCPkjGsAnA_cyJP7gOvErQlTXJRoCx-sQAvD_BwE&gclsrc=aw.ds)當初是直接利用FB登入所以`WHOIS`是hotmail, 結果我在 gmail 傻傻的等了一兩天甚至寫信給客服才發現...
成功之後在 [AWS ACM](https://console.aws.amazon.com/acm/home?region=us-east-1)應該可以看到剛剛申請的憑證狀態是綠色的 issued 字樣
{% asset_img acm_state_issued.png %}
在回到剛剛建立 Cloudfront 的表單那裡, 點選 Custom SSL Certificate, 點一下輸入欄應該就會彈出剛剛申請好的憑證可以選, 沒有的話請嘗試著刷新頁面和查看 ACM 狀態
{% asset_img custom_ssl_certificate.png %}
然後表單拉到最上方開始輸入幾個地方就好:
1. Origin Domain Name 彈出視窗點選自己剛剛部署好的 Bucket
2. Viewer Protocol Policy 選第二個可以把 http 導向 https
3. Compress Objects Automatically 選 yes 可以做資源壓縮, 就不需要自己對檔案做優化 gzip
4. Price Class: 可以選亞洲(Asia)比較便宜
5. Alternate Domain Names(CNAMES): 自己等等要設定的 DNS 例如我是 blog.kurthsu.tw
{% asset_img create_cloudfront_form1.png %}
{% asset_img create_cloudfront_form2.png %}
> 接著建立完成就可以看到剛剛創立的 Cloudfront, 他的 Domain name 是 `xxxxxxxx.cloundfront.net`, 拿著這個地址去自己的 DNS 創造一個 CNAME 然後指向這裡

最後, 點選剛剛創立的 Cloudfront(ID 可以點選), 點選 Origins and Origin Groups 標籤, 忘記一開始有沒有一筆 Origin Domain Name And Path, 有的話就編輯沒有的話就新增
{% asset_img edit_cloudfront.png %}
最後到剛剛部署的 S3 bucket 的 endpoint 地址輸入到 Origin Domain Name 就大功告成!
{% asset_img edit_cloudfront_form.png %}
S3 bucket 的 endpoint 在剛剛設定 Static website hosting 的地方
{% asset_img bucket_to_host_website.png %}

參考文章: [AWS S3 + CloudFront SSL 靜態網站架設教學](https://blog.niclin.tw/2018/06/17/aws-s3---cloudfront-ssl-%E9%9D%9C%E6%85%8B%E7%B6%B2%E7%AB%99%E6%9E%B6%E8%A8%AD%E6%95%99%E5%AD%B8/)

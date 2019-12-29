---
title: '[Devops]ssh alias server'
date: 2019-12-29 14:33:54
categories:
 - [Devops]
tags:
 - Devops
---

以AWS EC2為範例, 正常來說我們要在本地端連機台需要用pem檔執行:
`$ ssh -i ec2.pem ubuntu@ec2-ip`

目標是想只簡單的輸入
`$ ssh my-website`

## 實作
1. 把pem檔移到 ~/.ssh 目錄底下(或自己想要另外路徑管理也行)
2. `$ vi ~/.ssh/config`
```
Host my-website
  HostName ec2-ip
  User ubuntu
  IdentitiesOnly yes
  IdentityFile ~/.ssh/ec2.pem
```
大功告成~嘗試著用簡單的指令`$ ssh my-website`連連看吧

## 小補充
1. 當登入機台後如果有在機台上adduser,例如我創造了一個kurt, 並且把shh key放上去, 可以在`~/.ssh/config`簡單設定
```
Host my-website-by-kurt
  HostName 18.222.195.47
  User kurt
```
2. 雖然我們有設定User, 但依然可以用別的使用者去使用alias
`$ ssh other_user_name@my-website-by-kurt`
3. 這樣做不只是省指令這個好處, 假設有用load balence到多台機器並且與團隊說好都已apps這個user做管理, 就可以方便取名管理如下:
```
Host my-website-01
  HostName my-website-01.com
  User apps

Host my-website-02
  HostName my-website-02.com
  User apps

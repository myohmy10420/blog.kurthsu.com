---
title: '用Github來展示靜態網頁'
date: 2016-10-13 03:20:00
categories:
 - [生活, 跨行學程式]
tags:
 - Github
---
Github有一個免費建立靜態網站的功能，可以使簡單的作品上線，只能呈現簡單的HTML,CSS,JavaScript，其餘的功能幾乎是不行的唷！

## 教學
``` javascript
git init //初始化local git repository
git remote add origin [repository name] //local和remote做聯結
git pull origin gh-pages //把github上的東西抓下來
rm 不要的東西 //刪除我們不要的東東，也就是github預先幫我們產生的html和css檔
git add .
git commit -a
git branch gh-pages 新增一個local端的分支，叫gh-pages
git push origin gh-pages push上去github
```

產生出來的靜態網頁網址為：[github name].github.io/[repository name]
` 註：靜態網頁呈現的東西都是gh-pages這個分支的哦！！ `

參考網站：https://noootown.wordpress.com/2015/07/19/github-frontend/

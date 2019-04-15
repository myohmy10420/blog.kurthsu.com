---
title: Hexo 網站架設到 Github
date: 2019-04-15 22:00:51
tags: Hexo deploy Github
---

## 環境

node.js v8.11.2
git 2.9.2


### 安裝 hexo-cli

``` bash
$ npm install -g hexo-cli
```

### 本地端建立專案

``` bash
$ mkdir my_blog
$ cd my_blog
$ hexo init
$ yarn install
$ hexo g  # 產生 blog
$ hexo s  # 讓 blog 可以在 local 端檢視
```
現在應該可以到 [localhost:4000](http://localhost:4000/) 看到部落格

### 部署到Github上
先到Github上建立專案
再來回本地專案修改檔案最下面deploy的部分
``` yml _config.yml
deploy:
  type: git
  repository: git@github.com:[ github_username ]/repo_name
  branch: master
```
再來先安裝 hexo-deployer-git
``` bash
$ yarn add hexo-deployer-git
```
最後下指令部署上去
``` bash
$ git init
$ git add .
$ git commit -m 'init repo'
$ hexo d -g #產生靜態文件後，自動部署上 Github
```
檢查一下 Github 上是否有成功推上去

### 設置 Github 專案為靜態網站
Github很佛心的提供靜態網站的部署, 很適合快速架設 Hexo 可以自動產生產生靜態檔案的網站

1.到 Github 上剛剛的專案
2.點擊 Settings
3.拉到下方 GitHub Pages 標題
4.點擊 Choose a theme
5.隨便選一個主題按右上方 Select theme
6.成功後就會有到剛剛的 GitHub Pages 就會有Your site is published at: xxxx(URL) 的綠色訊息

### 客製化網址
請去購買一個網域, 這邊不教學怎麼買, 然後我是用godaddy買的網域

例如我購買的網域為 kurthsu.tw
我想要客製化網址為 [blog.kurthsu.tw](http://blog.kurthsu.tw/)

1.一樣到Github 上剛剛的專案 > Settings > 拉到 GitHub Pages 標題
2.有一個 Custom domain 內填入 blog.kurthsu.tw
3.到剛剛購買網域的 DNS 新增一個 CName > key(名稱)為 blog, value(值)為 [ github_username ].github.io

到剛剛客製化的網址應該就成功架設好嘍！

參考文章：
[Hexo網站架設＆筆記](https://sihhanwang.github.io/2019/04/09/hexo-tutorial/)
[GitHub Pages 也可拿來架設HTML靜態網站，與綁定網域名稱](https://www.minwt.com/website/server/18522.html)
和Github網站的一些訊息教學

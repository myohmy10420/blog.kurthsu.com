---
title: '[Vue2]iis伺服器引擎設定vue-router'
date: 2017-08-29 07:20:00
categories:
 - [Vue2, vuex]
tags:
 - Vue2
 - iis
 - vue-router
---
## 首先瞭解一下我的專案用了些什麼：
1. vue-cli安裝webpack-simple
2. 使用vue-router
3. vue-router使用history模式
4. 整個專案只用一個html檔案

## 遇到的問題點：
在本地端(8080port)的網頁除了使用<router-link>跳轉畫面之外，也可以直接手動輸入網址呈現vue-router該出現的東西，但是`在別的伺服器引擎上除非使用<router-link>跳轉畫面，不然直接手動輸入網址會出現404找不到網頁`。

這是因為直接手動輸入網址是會取檔案的靜態位置(真實位置)，而`vue-router的網址一律為動態位置(虛擬位置)`，所以伺服器第一反應是在靜態位置找不到檔案而回傳404頁面。

## 在iis伺服器引擎上的解決方法

在伺服器根目錄創造一個web.config檔案，他是整個伺服器最先讀取的檔案，可以`設定如果遇到什麼事情就去做哪些動作`：

```config
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="API SETTING" stopProcessing="true">
          <match url="(^v2/*)|(^api/*)" />
          <action type="None" />
        </rule>
        <rule name="Handle History Mode and custom 404/500" stopProcessing="true">
          <match url="(.*)" />
          <conditions logicalGrouping="MatchAll">
            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
            <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
          </conditions>
          <action type="Rewrite" url="index.html" />
        </rule>
      </rules>
    </rewrite>
    <modules runAllManagedModulesForAllRequests="true"/>
  </system.webServer>
</configuration>
```

`<action type="Rewrite" url="index.html" />`拿來設定如果找不到網頁則利用當前的URL去執行哪一隻檔案！

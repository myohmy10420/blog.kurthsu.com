---
title: '正法寶藏JavaScript第十四堂課(前端簡易資料處例express)'
date: 2016-10-04 07:00:00
categories:
 - [生活, 跨行學程式]
---
通常資料的處理和邏輯都是在後端實行，但這時我們還要找硬體創造一個資料庫，只是想做一個小功能的話我們不用這麼大費周章，今天的課程就是介紹express這個套件，實作一個簡易的會員註冊和登入系統。

##express環境
關於express的資訊可以直接參考他在[Github上的網站](https://github.com/expressjs/express)，其實在專案資料夾裡下$ npm install express --save後新增一個index.js的檔案，在裡面輸入官方給的基本啟動Code如下：

``` javascript
var express = require('express')
var app = express()

app.get('/', function (req, res) {
  res.send('Hello World')
})

app.listen(3000)
```
新增好後輸入$ node index.js就可以啟動了！

只是現在是最陽春的啟動方式所以畫面似乎什麼都沒發生，但只要沒出現error的話現在可以開啟網頁前往localhost:3000查看有沒有顯示Hello World了！

因為此堂課是用ES6裡jsx的語法，所以在安裝完可以使用jsx的環境之後再分享怎麼讓我們的啟動提示訊息不要這麼陽春。

## jsx環境
想要使用jsx語法只需要安裝三個套件，分別如下：
- babel-preset-es2015
- babel-preset-stage-0
- babel-register

只需要輸入一條指令就可以一次安裝：
$ npm install babel-preset-es2015 babel-preset-stage-0 babel-register --save

安裝好以後還要在根目錄建立一個檔案名稱為：.babelrc
裡面輸入程式碼：
``` javascript
{
  "presets": ["es2015", "babel-preset-stage-0"]
}
```

我們可以使用jsx的語法囉！再來我們把啟動伺服器的提示訊息改得更人性化一點吧。

把剛剛的index.js檔案內的程式碼改為如下：
``` javascript
import express from "express";

const port = process.env.PORT || 3000;
const app =  express();

app.get('/', function (req, res) {
  res.send('Hello World')
})

app.listen(port, () => {
  console.log('Example app listening on port %s', port);
});
```

現在執行$ node index.js的話會跳錯誤訊息說他不認識import這個東西，import其實就是jsx的語法，但我們剛剛不是安裝過了嗎？

其實是因為我們安裝了套件但是沒有跟檔案說要使用它，就好像一個樵夫買了斧頭要去砍材但是卻連斧頭都沒帶出門，這聽起來挺糟糕的，所以我們在根目錄多創一個檔案叫entry.js當作入口，輸入以下程式碼：
``` javascript
require('babel-register');
require('./index');
```

以後要開啟伺服器就從這裡開啟，在根目錄輸入$ node entry.js 應該就會看到Terminal裡面會有一行提示囉！

## 做第一個最基本的API去寫入和讀取檔案
我想要製作簡單的登入，註冊，登出等等頁面都很容易，重點是我們如何在註冊會員的時候把帳號密碼新增到一個檔案裡面，登入此會員帳號密碼的時候自動判斷是否通過，那途中有其他小工具可以加密密碼的防護機制就先不多作介紹。

這是註冊會員主要的核心功能程式碼：
``` javascript
const {username, password} = req.body;
//指定這個物件裡的東西要從別的地方獲取
const json = fs.readFileSync('passwd.txt', 'utf8');
//設定不可變的變數json去讀passwd.txt這隻檔案

let data = JSON.parse(json);  //let就是jsx裡的var

 if (username in data) {
    return res.send(`username ${username} already exists`);
  }
  //檢查有無重複使用者名稱

  if (username && password) {
    data[username] = {username, password: hashedPassword};
    const content = JSON.stringify(data);
    fs.writeFileSync('passwd.txt', content, {encoding: 'utf8'});
  //到這裡已經把使用者註冊的帳號密碼寫進passwd.txt檔案裡面了

    return res.send('Register Successfully.');
  }
  //顯示註冊成功
```

這是登入會員主要的核心功能程式碼：
```javascript
const {username, password} = req.body;
const json = fs.readFileSync('passwd.txt', 'utf8') || '';
const data = JSON.parse(json);
//用Json.parse的方法剖析變數data裡的東西
const row = data[username];

if (row && bcrypt.compareSync(password, row.password)) {
  return res.send(`Login Successfully. Hello ! ${username}`);
}
```

express有很多新語法可以參考下面兩個官方網站：
[官方原文說明](http://expressjs.com/)
[官方中文說明](http://expressjs.com/zh-tw/guide/using-middleware.html)

最後心得是資料接來接去的腦袋邏輯真的要超級清楚啊！！！

---
title: '[Git]Git & Github退回指定版本簡易作法'
date: 2018-04-12 15:06:00
categories:
 - [Git]
 - [Github]
tags:
 - Git
 - Github
 - commit
---

狀況1.Git退回上一個或指定commit
狀況2.Git把剛剛修改的東西全數取消
狀況3.把剛剛git add追蹤的東西取消掉
狀況4.把已推上Github的commit倒退

## 狀況1.Git退回上一個或指定commit
---
先看commit的歷史紀錄
`$ git log`
可以看到commit是一堆亂數
此時有兩個方法退回之前的版本

`$ git reset HEAD~1`
退回上一個版本
也可以2,3,4這樣看想退幾步

`$ git checkout 94bf1215a415070c71908536c7bd379e9fbd7675`
這串亂碼是剛剛在git log裡面看到的
切過去之後他會要求你切新的分支出去

## 狀況2.Git把剛剛修改的東西全數取消
---
最簡單的指令
`$ git checkout .`
可以把剛剛修改過尚未紀錄準備上commit(git add)的檔案全數還原
那如果不小心git add檔案的話呢？
看狀況3

## 狀況3.把剛剛git add追蹤的東西取消掉
---
`git  reset`
或者可以指定檔案
`git reset filename`

## 狀況4.把已推上Github的commit倒退
---
我們已經會在本地端的Git操控版本退回去了，但如果已經上Github的話呢？
`Github只能繼續往下走不能往回走！`
所以最簡單的做法就是push force

在本地端退回到想要回去的版本之後下
`$ git push -f`
就會把當前的版本全數覆蓋過去不論Github上面的所有版本紀錄如何
`記得這是無法復原的！！`請慎用

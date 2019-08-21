---
title: '[Linux]Linux 權限管理'
date: 2018-04-27 10:15:00
categories:
 - [Linux]
tags:
 - Linux
---
在租用雲端服務的時候像linode會空給一台機器自行管理，其實跟當初自己買mac一樣只是mac可以直接有很多介面化的操作，而當我們要去管理雲端機器的時候大多只能用terminal去下指令，這時候熟悉Linux的基本指令就很重要。

而權限管理也是保護機器上資料重要的一環，不能讓別人隨意取得root權限，不然其他人就可以肆意的控制這台機器了！

## 關於root
---
如果申請好雲端機器可以直接在terminal用ssh遠端登入：
`$ ssh root@xxx.xxx.xxx.xx`

在機器裡面可以創造很多使用者，`root則是內建且一開始擁有所有權限的`，所以我們創造一個使用者叫kurt：
`$ adduser kurt`
頭兩次是設定密碼和確認密碼，設定完之後一直按enter即可。

P.S:`$ sudo adduser --disabled-password username`可以創造一個沒有密碼的用戶，用SSH key登入更安全，可以參考這篇：
[SSH key 自動驗證到自己的雲端機器上](http://myohmy10420-blog.logdown.com/posts/7428801)

`$ exit`可以logout回到本地端，此時可以直接用kurt這個身份登入：
`$ ssh kurt@xxx.xxx.xxx.xx`

當然也可以在這時候隨意切換其他使用者，root也行只要知道使用者密碼的話：
`$ su - root`
如果要離開一樣：
`$ exit`
這時候是會彈回kurt這個使用者，在下一次exit才會回到本地端唷！


## sudo 权限
---
假設我們在root創了一個檔案abc.txt，然後想用kurt指令去修改該檔案：
`$ vim /root/abc.txt`
會跟你說沒有權限修改`Permission denied`的錯誤訊息。

這時候要登回root去改實在太麻煩了，可以利用指令把kurt擁有sudo權限：
`gpasswd -a kurt sudo`

此時要改檔案就下：
`sudo vim /root/abc.txt`
就擁有權限可以改動此檔案了


## 更改檔案權限
---
首先在root下查看此目錄中的檔案權限：
`$ ls -la`
![螢幕快照 2018-04-27 下午6.00.19.png](http://user-image.logdown.io/user/18810/blog/18323/post/7427437/1AoC4me4TRWWrChPPQua_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202018-04-27%20%E4%B8%8B%E5%8D%886.00.19.png)

第二個root是檔案擁有人
第三個root是群組，預設跟自己的名字是一樣的

再來前十碼第一碼`d`是目錄`-`是文件
再來第二碼開始每三碼代表一個權限，分別是自己的，群組的，其他使用者的
`r`代表可以讀`w`代表可以寫入`x`代表可以執行`-`代表沒有權限

如何更改檔案擁有人和群組？
`$ chown kurt:kurt abc.txt`
將abc.txt這個檔案所有人設定為kurt，群組設定為kurt。

如何更改檔案使用權限？
`chmod 644 abc.txt`
會把這個檔案變為
`rw-r--r--`

這裡
r=4
w=2
x=1
所以第一個數字是6 = 2 + 4代表`rw-`
第二個單純的是4代表`r--`

想知道如果利用SSH KEY免打密碼登入到自己的雲端機器請參考這篇：
[SSH key 自動驗證到自己的雲端機器上](http://myohmy10420-blog.logdown.com/posts/7428801)

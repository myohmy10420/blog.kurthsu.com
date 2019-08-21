---
title: '[Rails]image file in asset or public?'
date: 2018-04-22 13:55:00
categories:
 - [Rails]
tags:
 - Rails
 - Asset Pipeline
 - public
---
網上查了很多文章，一直對於圖片檔案要放在app/asset/images裡面還是public裡面感到很疑惑，應用起來也踢到很多鐵板，這邊把自己的問題記錄下來，也可以做最簡單的應用！

##### 什麼時候放在app/asset/images裡面？
>幾乎所有的圖片都放在asset裡面就好了，之後搭配關於`Asset Pipeline`的`$ rake assets:precompile`指令即可，後面會再提到。

##### 什麼時候放在public裡面？
>其實public也不止能拿來放圖片，他有點像一個倉庫`專門放置靜態頁面的東西`。

>靜態頁面大多是指404.html找不到頁面或者是500.html伺服器有狀況比較少用到且沒有什麼功能的頁面，此時給予這些靜態頁面的圖片放置在這裡比較正確。

## 實作
---
假設我們在asset/images裡面放了一個圖片image.jpg:

用scss引入當background
``` css stylesheet
background-image: image-url("imag.jpg");
```

當作圖片的話可以直接在view裡寫上rails的helper:
``` html view
<%= image_tag("imag.jpg", class:"images") %>
```

`如果有錯誤的話嘗試換上雙引號`

## Asset Pipeline
---
Asset Pipeline是什麼？可以直接先參考[Rails 實戰聖經](https://ihower.tw/rails/assets-pipeline.html)。

白話的說Rails會自己把所有css和javascript壓縮成一個檔案不製造過多的網頁request。
`處理圖片的時候會給圖片名稱一段編碼，以後只要更新過圖片即使是一模一樣的也不會造成catch！`

請檢查自己deploy的環境，例如本地端或者是上heroku是不用自己下指令壓縮的！他在執行伺服器的時候就會自動執行，不然正常來講需要下指令：

`$ rake assets:precompile`
這時候檔案會產生在public/assets/下。

當然要刪除的話就下：
`$ rake assets:clobber`

#### 實戰聖經補充：
>注意，如果在開發模式下執行了rake assets:precompile，那麼因為放在public/assets/下的靜態檔案會優先丟給瀏覽器，所以這時候再修改app/assets下的原始碼會沒有作用。所以，開發時請記得要刪除這個目錄。

參考文章：
[Rails 實戰聖經](https://ihower.tw/rails/assets-pipeline.html)

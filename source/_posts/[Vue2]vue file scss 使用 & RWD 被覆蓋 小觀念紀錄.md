---
title: '[Vue2]vue file scss 使用 & RWD 被覆蓋 小觀念紀錄'
date: 2017-09-01 08:12:00
categories:
 - [Vue2, vuex]
tags:
 - Vue2
---
## 專案上的應用
1. 使用vue-cli webpack-simple
2. 使用vue-router呈現頁面所以大部份的view由.vue檔案組成
3. 每個.vue檔案裡面直接在裡面寫自己的css
4. html還是有引進大家共用的scc(由sass compass出來的css)


## .vue檔案裡不能對外部的css檔案使用function
其實這觀念很簡單的，因為我們引入的已經是css檔案，自然.vue檔案不能使用@extend等sass才可以用的function去銜接他XD

但.vue檔案裡面還是能直接使用外部檔案的css喲！

## RWD要寫在.vue檔案的最下面
正確寫法：
``` css
.someElement {
  display: block;
}
@media only screen and (max-width: 760px) {
  .someElement { display: none; }
}
```
錯誤寫法：
``` css
@media only screen and (max-width: 760px) {
  .someElement { display: none; }
}
.someElement {
  display: block;
}
```
如果使用錯誤寫法，我們可以在chrome的檢查工具上會發現RWD的東西即使被觸發顯示了，但還是會被下面原本someElement的東西蓋過去哦！
`千萬不要太濫用!important這個猛藥！`

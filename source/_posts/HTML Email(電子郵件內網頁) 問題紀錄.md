---
title: 'HTML Email(電子郵件內網頁) 問題紀錄'
date: 2017-03-21 06:47
categories:
 - [HTML Email]
tags:
 - HTML Email
---
# 麻煩的outlook電子郵件網頁
繼{% post_link 'HTML Email(電子郵件內網頁)' '上一篇文章' %}後續還是踩到了一些雷，再來紀錄一下。

### line-height
上篇文章有提到這個屬性還被大多數瀏覽器和信箱支持，但在用程式開啟的outlook裡面它不是不支持，而是如果寫在<table\>這個tag裡的話他不支持(囧)。

也就是說瀏覽器打開的話下面例子是ok的：
``` html
<table style="line-height:20px;">
    <tr>
        <td>element</td>
        <td>element</td>
    </tr>
</table>
```
但在程式打開的outlook不支持寫在<table\>裡所以我們要改寫成：
``` html
<table>
    <tr>
        <td style="line-height:10px;">element</td>
        <td style="line-height:10px;">element</td>
    </tr>
</table>
```
可不可以寫在<tr\>上我還沒測試過，因為每一次測試都是要從主管那更新所以我不好意思一直測試XD，再來在同一個<tr\>的tag裡面是否只需要寫一個<td\>就好也不得知，總之照著個方式寫是ok的哦！

### 超連結<a\>的文字顏色
如果想要改變點下超連結之後的顏色，也就是做到Visited的動作以前我們可以用選擇器來辦到：
``` css
.somelink:visited {
    color: #fff;
}
```
但是inline style卻沒有這種寫法，我們只能在tag裡面使用!important強制讓他一直維持一種顏色：
``` html
<a style="text-decoration:none; color: #fff !important;" href="#">element</a>
```
[參考這篇Stack overflow](http://stackoverflow.com/questions/9162362/how-to-write-avisited-in-inline-css)

當然在遊覽器上看都沒問題，只不過問題又出在程式上開啟的outlook....，我用線上產生的html可行(國外有網站是可以線上排版後幫你產生html)但自己做的html卻失敗，於是我直接把按鈕做成一個圖片檔放上去了。

總之最後終於把html email告一段落了～

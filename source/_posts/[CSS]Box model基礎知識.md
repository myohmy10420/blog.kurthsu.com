---
title: '[CSS]Box model基礎知識'
date: 2016-12-04 10:22:00
categories:
 - [CSS]
tags:
 - CSS
---
我們可以視一個tag包起來的區塊(通常為div)為一個Box

#### 了解Box model有什麼東西
- content: 內文凡指文字，圖片等都稱為content。
- border: content的邊界用border包起來，就像是漫畫的格子一樣。
- padding: border到content的距離。
- margin: Box和Box之間的距離。

#### block(區塊元素)和inline(內聯元素)的差別
我曾經以為每個tag包起來的東西都會是一個區塊，但當我在切版的時候遇到一些莫名的排版走掉問題，一樣的CSS屬性和位置設定卻是出現不一樣的效果，原來tag自己有分兩個種類，又各自原本的原生屬性，也找到一篇很詳細完整的資料就給大家直接參考吧！

轉載痞客邦文章：
[[CSS教學] - CSS基礎您不能不知道的block(塊元素)、inline(內聯元素)差別](http://injerry.pixnet.net/blog/post/38847454-%5Bcss%E6%95%99%E5%AD%B8%5D---css%E5%9F%BA%E7%A4%8E%E6%82%A8%E4%B8%8D%E8%83%BD%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84block(%E5%A1%8A%E5%85%83%E7%B4%A0))

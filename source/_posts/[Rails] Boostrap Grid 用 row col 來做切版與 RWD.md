---
title: '[Rails] Boostrap Grid 用 row col 來做切版與 RWD'
date: 2018-04-18 15:00:00
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - Boostrap
 - Grid
 - RWD
---
參考文件:
[bootstrap-sass](https://github.com/twbs/bootstrap-sass)
[Official Sass port of Bootstrap 2 and 3.](http://getbootstrap.com/css/#sass)

`這個套件是搭配boostrap3`，想要用更新的boostrap的話[官方文件](https://github.com/twbs/bootstrap-sass)README.md的最上方有提及。

關於bootstrap-sass的安裝方法可以參考[這篇](http://blog.kurthsu.tw/posts/7333490)

## Grid system
---
[Grid system](https://getbootstrap.com/docs/3.3/css/#grid )

使用方法:
``` html index.html.erb
<div class="row">
	<div class="col-xs-6">line1</div>
	<div class="col-xs-6">line1</div>
	<div class="col-xs-6">line2</div>
</div>
```
`row`是固定把col包起來的，也只有包起來col才會生效。
`col`可以設定總共佔這行的幾格，總共是12格。

`每個row總共都會被切成12格，如果一行之內的col超過12格他將會自動換行變到第二行row`
>如上面例子col的class會帶數字，就是佔幾格的意思，前兩個col加起來剛好12塞滿第一行，而第三個row就會被換到下一行去了！

較特別的例子如:
``` html index.html.erb
<div class="row">
	<div class="col-xs-4">line1</div>
	<div class="col-xs-9">line2</div>
	<div class="col-xs-2">line2</div>
</div>
```
可以看到第二個row開始因為4+9 > 12所以他已經到第二個row，而9+2 < 12則第三個col也會在的第二個row上。

## RWD
---
以這個例子舉例:
``` html index.html.erb
<div class="row">
	<div class="col-xs-4">row</div>
	<div class="col-sm-12 col-md-8">row</div>
</div>
```
第一個col中間狀態是xs代表不論遊覽器或裝置畫面大小(與css沒關係喲！)他永遠佔了四格。

第二個row如果符合sm畫面大小的範圍他會是佔12格變成第二個row，如果符合的是md的畫面大小的話則只會佔8格此時會跟第一個col同個row。

那狀態有哪些呢？

`xs`-Extra small的簡寫-畫面\<768px
`sm`-small的簡寫-畫面>=768px
`md`-Medium的簡寫-畫面>=992px
`lg`-large的簡寫-畫面>=1200px

如果我設計的一個col
<div class="col-sm-6 col-md-4 col-lg-3"\>something<div\>
代表畫面寬度
768px~991px是6格
992px~1199px是4格
\>=1200px是3格
`此時畫面如果<768則會佔滿12格！`當然你也可以把col-xs設定進去此時佔多少格。

grid還有幾個例子可以注意:
``` html index.html.erb
<div class="col-md-4">something<div>
```
代表畫面寬度
<991是12格
\>=992px是4格
`此時即使畫面超過了1200px也是只佔4格唷！原因是因為他的css沒有被col-lg再覆蓋上去了`

如果想一直固定的話可以這樣做:
``` html index.html.erb
<div class="col-xs-4">something<div>
```

原理可以參考官方這個
``` scss
@media (max-width: @screen-xs-max) { ... }
@media (min-width: @screen-sm-min) and (max-width: @screen-sm-max) { ... }
@media (min-width: @screen-md-min) and (max-width: @screen-md-max) { ... }
@media (min-width: @screen-lg-min) { ... }
```

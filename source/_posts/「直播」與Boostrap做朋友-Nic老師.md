---
title: '「直播」與Boostrap做朋友-Nic老師'
date: 2017-04-24 13:33:00
categories:
 - [生活, 上課]
tags:
 - Boostrap
---
## 什麼是Boostrap?
>快速開發，不用寫太多css和html
>目前最火熱的套件之一
更詳細的介紹可以直接在網上收尋bootstrap component即可，有中文版，但是不能複製代碼所以不好使用。
版本建議使用bootstrap3，4還不穩定而2又太舊。

## 如何測試
Nic老師今天使用panels做介紹，建議直接先複製官方網站的代碼到自己的程式碼上面看看效果是不是跟官方網站和自己想要的效果一樣，再把自己的程式碼帶進去。

而在一開始複製官方網站測試結果的時候希望不要被自己之前的code干擾，可以先把所有code進行註解，方法：
`command + a先全選自己的程式碼 >>>> command + /可以進行全部註解 >>>> 找個地方把剛剛複製的程式碼貼上 >>>> command + s存擋 >>>> 到遊覽器commad + r刷新頁面看結果。 `

開發貼心小技巧：
- command + x為剪下，這樣就不用複製代碼貼上後再回去刪除舊的代碼。
- 在atom裡面只要打上div後按下tab會直接幫你做好一個div的tag。

#### 示範的經過：
套上去之後上下左右沒有白邊，所以要自己改一下nav bar的css，讓他留下白邊，可是可能會造成另一個頁面有多餘的空白，可以使用`margin-top: -20px;`試試看效果如何，在調整上下的話負數的px是把區塊往上調，正數的px則為往下。

按鈕也有的元件可以套，收尋buttom或者是簡寫的btn都找得到，示範如下：
`<% f.submit "Submit", class: 'btn btn-danger' %>`

想要讓左右邊空白大一點或者是要呈現的區塊窄一點可以套上Bootstrap的格線系統`col-lg-8`並且自己做一個讓他置中的屬性，Nic老師做了一個centered的class並且給他`margin: 0 auto`;，要記得使用Bootstrap的格線系統一定要拿一個`container`去包住他：

```html index.html
<div class="container">
	<div class="row">
		<div class="col-lg-8 centered">
			something...
		</div>
	</div>
</div>
```
```css style.css
.centered {
    margin: 0 auto;
}
```

開發貼心小技巧：
- 做好一個區塊就刪除舊的，隨時看結果和更新程式碼，一步一步來才不會亂。

## 格線系統：
>可以快速的做出自適應網頁

一開始Nic老師建議我們把這四大常用尺寸記下來：
- XS超小螢幕-最常使用在手機
- SM小螢幕-最常使用在平板
- MD正常螢幕-最常使用在筆電
- LG最大螢幕-最常使用在桌機那種正常螢幕

直接試試看如何使用：
```html index.html
<div class="container">
	<div class="row">
		<div class="col-xs-12 col-md-3 col-lg-8 borderBox">
			something...
		</div>
	</div>
</div>
```
```css style.css
.borderBox {
    border: 1px solid;
    height: 50px;
}
```
直接對著遊覽器拉縮左右的寬度應該就會呈現效果。

格線系統一行分為12格，所以一行可以有2個6並排，或者3個4並排，或者2個3再加上1個6並排，以此類推最多一行有12格，高過則會被往下擠做換行動作，當然也可以像一開始的範例讓他一行只要用到8格再搭配置中。

開發貼心小技巧：
- 打開chrom的開發者工具，拉縮遊覽器時右上角會顯示現在寬高有多少px。
- 開發者工具可以直接對CSS做測試不會影響到原來的程式碼，調整好之後直接把東西複製到自己的code裡面，就不用一直做存擋刷新的動作了。

課程最後補充：
chrome的擴充套件`Full Page Screenshot`可以截網站全畫面，直接在收尋欄上照著打Full Page Screenshot就找得到。

### Q&A:
>這邊老師講很快我只憑印象和我的理解記錄下來，只能當作參考唷！

1.雖然不用背Boostrap和一些html的code，但有沒有更快速上手開發的方法？
    多練習。

2.為什麼Boostrap有很多在CSS沒看過的代碼？
    那是Boostrap框架自己的代碼。

3.不知道怎麼對應html和css，寫到後面很容易亂掉，有什麼辦法可以解決？
    CSS的命名很重要。

4.很難實現自己網頁想要的style，不然就要花很多時間，尤其是收尋功能，怎麼辦？
    收尋功能多上討論區看別人怎麼做，很多學長姐都有分享，在一開始想要做出自己想要的畫面花很多時間是很正常的事情。

5.寫code寫到很煩怎麼辦？
    真的很煩就出去走走，讓自己散心一下吧。

6.git branch搞砸了怎麼辦？
    分支要針對性大一點的開，不然一亂掉很麻煩，還有要開新的分支一定要回master開，不然從分支直接新開分之會是從那個分支再開分支出去，這樣很難管理。

7.排版是怎麼做的？
    不明確的問題，建議熟悉Boostrap元件，多使用，甚至手畫出想做的網站再慢慢做出來。

8.放大縮小網頁怎麼做？
    command + + = 放大
    command + - = 縮小

9.分隔系統圖片會爆炸怎麼辦？
    `給圖片下一個class給他Boostrap的img-responsive屬性可以讓他有自適應。`

10.想要Boostrap的動態功能有沒有辦法簡單做出來？
    比較沒有，也比較不可能只針對一個功能做一個框架，比較小和特別的東西一定要自己做出來。

11.開發者模式怎麼讓他自己一個視窗？
    在開發者模式的視窗有選項，可以點分離的圖示就會變成獨立視窗。

12.怎麼用兩台電腦同時開發一個專案？
    可以使用google雲端dropbox之類的工具。

13.jumptron和box有什麼差別？
    jumptron是Boostrap的套件，而box是老師自己寫的CSS

感謝Nic老師讓我們快速認識了Boostrap這個框架，果然線上閱讀文件的能力很重要嗄XD，今天老師也是帥帥der!

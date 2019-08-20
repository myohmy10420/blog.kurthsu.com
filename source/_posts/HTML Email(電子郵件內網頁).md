---
title: 'HTML Email(電子郵件內網頁)'
date: 2017-03-20 08:03:00
categories:
 - [HTML Email]
tags:
 - HTML Email
---
# HTML Email
這陣子第一次碰到要做一個可以當Email發送的HTML檔案，感覺有中文解說的網站不多(也許是我關鍵字下得不好)，總之如果打HTML Email是可以找尋到國外不少資源，再來介紹我當中遇到的每個地雷。


### 完全使用inline style控制style
第一次遇到這個工作時我天真的以為不就跟平常瀏覽器用的HTML一樣把HTML和CSS做分離，設計師還出圖的很簡單應該一下做完了。

沒錯是一下就做完了，但是完全不能使用！！！

因為在每個個大郵件他們都有自己的CSS，所以會把你傳送檔案裡有CSS的屬性大多都會擋掉，所以要考慮各大瀏覽器的相容度之外，每個郵件的相容度是不一樣的(gmail, outlook打開的效果不一樣)，所以要回歸最原始最基本的做法：
```html
<div style="width: 100px;height: 100px;border: 1px solid black;"></div>
```
以上就是inline style的寫法，記得完全避諱使用class，雖然網路上有些人似乎有成功使用選擇器去input(就是寫class的做法)，但我自己試過幾個是失敗的。

### 盡量完全使用table
為什麼要用table呢？因為在email中很多的CSS屬性是不相容的，像是position, float等竟然不能使用啊！變成說我們的切版只能實實在在地用table硬刻出來了，雖然我們的padding, margin在大部分的郵件還是有效的，但如果是要寄給outlook的郵件就不建議使用了！待會會解釋。

### 特殊的形狀一律使用圖片取代
什麼意思呢？
因為我們的border-radius, rotate等等也都不太能使用了，所以有什麼特殊的圖形盡量上傳到網路上用`<img src="#">`這個tag去實現吧！

### 重新認識基本的table屬性
雖然table可以稍微切版，形狀可以直接使用圖片，但平常我們要把東西調精準的話table一定不夠啊！一定要搭配margin和padding甚至position等等，為什麼不能使用呢？

剛剛提過position是完全不用考慮了，但如果要寄給outlook的話margin和padding也是不建議的哦！那怎麼辦？我們還有原始的table屬性可以使用！
- `cellpadding`
- `cellspacing`
- `border`
- `line-height`
- `align`
- `valign`
- `colspan`
這幾個原生屬性要好好利用哦，可以把內文位置再加以調整的工具不多了啊！
所以說HTML Email的版面也不要太複雜了

大部分前四個屬性我都會直接灌在table上面了
```html
<table style="width: 100%;font-size:14px;line-height:30px;" cellpadding="0" cellspacing="0" border="0">
</table>
```
再搭配chrome的開發者工具就很好先做初始調整了。

### 非瀏覽器開啟的outlook不吃<div\>這個tag
`非瀏覽器開啟的outlook不吃<div>這個tag`
`非瀏覽器開啟的outlook不吃<div>這個tag`
`非瀏覽器開啟的outlook不吃<div>這個tag`
這個太哭夭了我要講三遍，如果有在用公司郵件的大多都會接觸到這個工具，就是outlook不一定在瀏覽器上開啟，他本身就有一個程式是打開郵件的，這個程式不吃<div\>這個tag！！！
也就是說在<div\>裡面加上的style會完全忽略！千萬要小心！

### 總結
所以如果要做大部份郵件、瀏覽器甚至是程式打開的outlook都正常的HTML Email，照著上面的步驟應該就不會出太大的問題摟！

至於有沒有更多的屬性可以用或更好的做法那肯定是有的，只是我就沒再多做研究下去囉！這是我第一次遇到的HTML Email功課，希望能幫大家能避開一些地雷。XD

還有續集~{% post_link 'HTML Email(電子郵件內網頁) 問題紀錄' '請點我看文章' %}。

**3/22補充：感謝FB上[Front-End Developers Taiwan](https://www.facebook.com/groups/f2e.tw/?fref=ts)社團有人分享的[HTML Email適應表網站](https://templates.mailchimp.com/resources/email-client-css-support/)**

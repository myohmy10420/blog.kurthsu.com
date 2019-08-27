---
title: '[HTML]<![endif]-->Conditional comments指定IE版本顯示內文'
date: 2018-10-27 23:53:47
categories:
 - [HTML]
tags:
 - HTML
 - IE
 - Conditional comments
 - endif
---
有些內文只想在特定的IE遊覽器開啟時作用, 就可以使用這招

確定safari的開發模擬IE不成功, 建議是真的想辦法借台window系統用IE測試吧XD
```
<p class="accent">
<!--[if IE]>
According to the conditional comment this is IE<br />
<![endif]-->
<!--[if IE 6]>
According to the conditional comment this is IE 6<br />
<![endif]-->
<!--[if IE 7]>
According to the conditional comment this is IE 7<br />
<![endif]-->
<!--[if IE 8]>
According to the conditional comment this is IE 8<br />
<![endif]-->
<!--[if IE 9]>
According to the conditional comment this is IE 9<br />
<![endif]-->
<!--[if gte IE 8]>
According to the conditional comment this is IE 8 or higher<br />
<![endif]-->
<!--[if lt IE 9]>
According to the conditional comment this is IE lower than 9<br />
<![endif]-->
<!--[if lte IE 7]>
According to the conditional comment this is IE lower or equal to 7<br />
<![endif]-->
<!--[if gt IE 6]>
According to the conditional comment this is IE greater than 6<br />
<![endif]-->
<!--[if !IE]> -->
According to the conditional comment this is not IE 5-9<br />
<!-- <![endif]-->
</p>
```

## 注意一下特殊語法
gt: 大於
lte: 小於或等於
最後非IE遊覽器的寫法比較特別
```
<!--[if !IE]> -->
According to the conditional comment this is not IE 5-9<br />
<!-- <![endif]-->
```

參考文章:
[Conditional comments](https://www.quirksmode.org/css/condcom.html)

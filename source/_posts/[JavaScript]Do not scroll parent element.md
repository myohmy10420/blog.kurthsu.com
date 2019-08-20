---
title: '[JavaScript]Do not scroll parent element'
date: 2017-09-18 06:50:00
categories:
 - [JavaScript]
tags:
 - JavaScript
 - scroll
 - parent
 - element
---
正常來講滑完子元素的滾輪他會自動滑外面父元素的滾輪，要取消的話只要加上一段js就可以了：

```javascript
$( '.son' ).on( 'mousewheel', function ( e ) {
	var event = e.originalEvent,
		d = event.wheelDelta || -event.detail;

	this.scrollTop += ( d < 0 ? 1 : -1 ) * 10;
	e.preventDefault();
});
```

比較有趣的是`this.scrollTop += ( d < 0 ? 1 : -1 ) * 10;`後面的`* 10`似乎會影響滑動速度，調越高滑越快，還沒有很搞懂這個原理之前先把它記下來。

參考文件
某一個人的[Stack overflow](https://stackoverflow.com/questions/25125560/prevent-parent-scroll-when-in-child-div)

[某人的codepen](https://codepen.io/LelandKwong/pen/edAmn)也很值得研究

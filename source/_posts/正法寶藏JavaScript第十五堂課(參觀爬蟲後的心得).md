---
title: '正法寶藏JavaScript第十五堂課(參觀爬蟲後的心得)'
date: 2016-10-08 05:45:00
categories:
 - [生活, 跨行學程式]
---
重複性高的事情總是有些聰明的人可以把它變得簡單又自動化，像JS只又要重複性的動作通常就會提出來做一個重複使用的物件，其實只要是在電腦上的重複性動作幾乎都可以做到這樣的事情，即使是人為行為！

## 發掘重複行為
今天上課的主題蠻有趣的，有些人會喜歡去論壇收尋帥哥美女的圖片，試想我們重複的行為有什麼？

- 點網址進去
- 看看圖片內容
- 下載圖片
- 點另外一個網址

這次的專案其實算是超過我的程度太多了，XD所以跟大家分享一些重點就好，以下是老師使用ES6的語法～

1.載入網址：
``` javascript
const regexpUrl = new RegExp(`(https?:)*//[^/\\s]+/\\S+\\.(${imageTypes.join('|')}|js)`, 'gi');
```

2.查看圖片內容的方法可以下載一個叫 ` image-size ` 的套件去過濾不符合相片的圖片大小。

3.下載位置路徑可以用 ` mkdirp `這組套件，程式碼如下：
``` javascript
import mkdirp from 'mkdirp';

export default function(path) {
  return new Promise((resolve, reject) => {
    mkdirp(path, (err) => {
      err ? reject(err) : resolve();
    });
  });
}
```
還有太多東西還不是很熟悉，但我不得不說爬蟲真的是很方便的東西，譬如說我們不是下載圖片，而是在ＦＢ取消所有與前任的追蹤等等XD，而且也會突然認識到許多套件，這個世界有太多聰明的人呀！

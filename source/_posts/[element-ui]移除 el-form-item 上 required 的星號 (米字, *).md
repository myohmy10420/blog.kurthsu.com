---
title: '[element-ui]移除 el-form-item 上 required 的星號 (米字, *)'
date: 2017-09-04 14:36:00
categories:
 - [element-ui]
tags:
 - element-ui
 - el-form-item
 - required
---
其實在網路上真的不好搜尋，也許是我關鍵字下的不好，所以這是我自己想辦法直接硬改css就好XD

## 簡單解決方法
```css
.el-form-item__label::before {
	content: '' !important;
}
```

#### 題外話紀錄
el-form-item在官方文件上有一個自己也有一個required的屬性可以寫在html上，有人說這個單純是控制那個星號(米字, *)的跟實際驗證無關，但我自己用是有關聯的！記錄以下幾個狀況：

1. 不設定他也不給他rule的話預設值是false，所以required的驗證模式是關閉的。
2. 要設定他的話在html上打`:required = 'true'`，就會開啟驗證模式摟！此時會有星號了。
3. 如果不設定他但是rule有required這條規則的話也會把星號加上去喔！

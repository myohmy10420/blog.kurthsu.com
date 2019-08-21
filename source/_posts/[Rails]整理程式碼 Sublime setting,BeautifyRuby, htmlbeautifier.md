---
title: '[Tool]整理程式碼 Sublime setting,BeautifyRuby, htmlbeautifier'
date: 2018-04-17 08:02:00
categories:
 - [Tool]
tags:
 - Tool
 - Sublime
 - BeautifyRuby
 - htmlbeautifier
---
## 工具的官方文件
[Sublime text](https://www.sublimetext.com/)
[BeautifyRuby](https://github.com/CraigWilliams/BeautifyRuby)
[HTML Beautifier](https://github.com/threedaymonk/htmlbeautifier)

## Sublime setting
---
快捷鍵`command + ,`
或者點選左上角`Sublime Text > preferences > settings`

開啟後會有左右兩個分割視窗，注意他們各自的檔案名稱:
`Default是預設值，不要隨意改動！`
`User`就是我們設定的地方，他已經預設有所有Default值的內容，設定的方法是用User的東西去覆蓋Default的東西，因為sublime會去讀User這隻檔案的設定。

關於排版的只介紹下列四個:
1.`font_size` 字體大小
2.`tab_size` 按一下tab會有幾個空白鍵
3.`line_padding_bottom` 每行下面的間隔
4.`line_padding_top` 每行上面的間隔

我的設定如下，把整個複製貼上就行:
``` javascript Preferences.sublime-settings — User
{
	"tab_size" : 14,
	"tab_size": 2,
	"line_padding_bottom": 6,
	"line_padding_top": 6,
}
```
存檔後應該會立即見效



## BeautifyRuby
---
[BeautifyRuby](https://github.com/CraigWilliams/BeautifyRuby)
安裝方法:
1.`command + shift + p`打開選單
2.輸入install後選取`Package Contol: Install Package`
3.輸入`BeautifyRuby`安裝此套件

這時候對model, controller那些檔案試著下`command + ctrl + k`試試看有沒有成功

再來如何設定BeautifyRuby:
1.`command + shift + p`打開選單
2.輸入package list選取`Package Contol: List Packages`
3.輸入`BeautifyRuby`找到此套件的資料夾
4.找到`BeautifyRuby.sublime-settings`檔案把他拉到sublime上編輯

預設值應該為這樣:
``` javascript Beautify.sublime-settings
{
	// Would you prefer a tab or two spaces to represent a tab
	// The default is two spaces represented by 'space'
	// anything else will use one tab character
	"file_patterns": ["\\.html\\.erb", "\\.rb", "\\.rake", "Rakefile", "Gemfile", "Vagrantfile"],
	"html_erb_patterns": ["\\.html\\.erb"],
	"run_on_save": false,
	"save_on_beautify": true
}
```
可以加上"tab_size": 2
`因為BeautifyRuby的tab_size不是去吃Preferences.sublime-settings的哦！`然後他的註解有說明default是2

`file_patterns`可以看到它支援哪些檔案

## HTML Beautifier
---
[HTML Beautifier](https://github.com/threedaymonk/htmlbeautifier)
BeautifyRuby的文件上直接推薦HTML Beautifier這個套件，他是安裝在gem上的！
``` javascript Gemfile
gem 'htmlbeautifier'
```
執行`bundle install`

用法是在終端機上面下指定:
`$ htmlbeautifier file` 指定單一檔案
`$ htmlbeautifier file1 [file2 …]` 他可以指定多隻檔案以上
`$ htmlbeautifier app/views/folder/*` 指定一個資料夾的所有檔案
`$ htmlbeautifier app/views/**/*` 指定views這個資料夾的所有資料夾內的所有檔案
`$ htmlbeautifier app/views/**/*.html.erb` 指定views這個資料夾的所有資料夾內的所有.html.erb的檔案

`由於這種指令的是下了就無法復原建議htmlbeautifier前先commit哦！`

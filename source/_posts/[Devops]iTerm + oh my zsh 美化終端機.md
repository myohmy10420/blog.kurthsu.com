---
title: '[Devops]iTerm + oh my zsh 美化終端機'
date: 2018-08-01 09:44:00
categories:
 - [Devops]
tags:
 - Devops
 - iTerm
 - oh my zsh
---
[iTerm](https://www.iterm2.com/downloads.html)是比內建終端機還好用的終端機工具，因為可以客製化很多東西，搭配oh-my-zsh的套件能讓美觀更進一步，且可以直接套用主題～

假設已經安裝好iTerm了，我們參考[oh-my-zsh的官方文件](https://github.com/robbyrussell/oh-my-zsh)開始執行以下步驟：

首先要先看有沒有安裝好Zsh(`$zsh —version`可以檢查)

如果沒有安裝的話就用curl安裝

`$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)”`

安裝完之後重新打開iTerm如果視窗是顯示bash環境請轉換成zsh
`$chsh -s /bin/zsh`
重開後視窗應該要顯示zsh

安裝oh-my-zsh的東西
首先下載
`$ git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh`

我們要自己生成zsh的設定檔，位置是`~/.zshrc`，我們直接用官方的template比較省事
`$ cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc`

我們希望用的是agnoster主題，[官方有說明文件](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes#agnoster)
發現要用這個主題要用[Solarized顏色](http://ethanschoonover.com/solarized)和Powerline-patched Meslo字型(https://github.com/powerline/fonts)

然後可以在剛剛的`.zshrc`檔案裡頂端加入一行
`DEFAULT_USER=“xxxx”`，xxxx可以輸入自己的英文名字
再來找到一行ZSH_THEME這個是設定主題的，如果被註解掉就把它解開設定主題如下
`ZSH_THEME="agnoster”`，agnoster就是主題名稱

每次修改完.zshrc可以輸入`source ~/.zshrc`刷新一下這個檔案再重開iTerm

再來可以去設定字形和顏色，打開iTerm後左上角
`iTerm` > `Preference` > `Profiles`
 `Colors` > 可以設定顏色
`Text` > `Change Font` > 可以設定字型，建議設定Menlo for Powerline和14pt

應該就有漂亮的iTerm可以使用了！

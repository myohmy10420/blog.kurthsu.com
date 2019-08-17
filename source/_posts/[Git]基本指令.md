---
title: '[Git]基本指令'
date: 2016-10-27 14:53:00
categories:
 - [生活, 跨行學程式]
 - [Git]
tags:
 - Git
---
相信網路的世界都有聽過Github這個火紅的**平台**，這邊Git其實是**工具**，他本來就可以在本機端執行版本控制，而Github是把Git版本控制更發揚光大的**平台**！

## Git基本指令

`git init`在資料夾內安裝Git環境。
`git add (filename)`選擇要給Git監控的檔案，可以搭配Linux的指令收尋多個檔案，我會使用的方式是用**git add .**監控所有檔案，再搭配**.gitignore**檔案去Ban掉不需要監控的檔案。
`git -m commit "(commit name)"`為一個段落提交一個紀錄點，雙引號內的commit名稱需要多練習唷！很多時候commit一多而名稱嚇得不清楚的話後面維護會很麻煩。

#### 以上為正常流程最常使用的一套指令，再來介紹版本控制最大的魅力，當開發到一半發現專案做亂掉了怎麼辦？

`git reset HEAD^`可以把所有檔案回到上一次commit的狀態，所以特別建議新手每做一點小修改就下一個commit，讓自己多掌握自己作品的控管。
`git checkout .`這是蠻特別的用法，效果會跟上面一樣，小細節上就先不談。

#### 但如果像上述說的下了一堆commit，但我有一個想拿掉的功能已經下了數次commit該怎麼恢復？(意思就是重回一次以上的commit點)

`git log`查看所有commit記錄，這時候commit名稱下得清不清楚的好處就在這裡顯現囉！查看完之後可以按**q**離開。

`git reset HEAD~(number)`查完想回到的第幾個commit之後數字是回到前**幾次**版本。
例如git reset HEAD~2是回到前兩次，git reset HEAD~3則回到前三次，以此類推。

reset絕對要小心使用！

#### 在我們開發專案中，其實最好保持master(主支)的乾淨，所以要做任何新的功能最好開新的branch(分支)開發。

`git branch`列出目前有多哪些branch(只在本機端的)
`git branch -a`列出所有branch(包括遠端的)
`git branch (branch name)`產生新的branch
`git checkout (brach name)`切換到指令brach，這時可以開發新功能囉！
`git branch -d (brach name)`刪除指定branch
`git branch -D (brach name)`強制刪除指定branch

#### 如果做到一半必須先去別的分支做其他事情又不想亂下commit的話我們可以用git的暫存(stash)功能。

`git stash`把從最後一個commit到剛剛的東西暫存到暫存區裡，這時的專案狀態又會回到最後一次commit那邊
`git stash list`列出所有暫存區(stash)的資料
`git stash pop`取出最新的一筆並移除在暫存區(stash)的資料
`git stash apply`取出最新的一筆暫存資料但是暫存資料不移除
`git stash clear`把暫存區(stash)的東西都清掉


#### Github提供的遠端平台，可以讓大家使用Git共同開發一個專案，稍微熟悉的話對新手算是加分哦！先介紹自己管理自己的專案。

`git remote add origin git@github.com:username/(repository
 name).git`指定遠端的地址，使用Github創造新專案(repository
)的時候就有清楚的引導囉！
`git push`在設定完地址和下完commit的時候即可使用這個指令上傳到剛剛設定的Github專案上，要注意的是版本狀態必須是最新的，也就是說是下完commit之後才能上傳，一個檔案都不能有修改過。
`git status`查詢有哪些檔案是修改過的，可以用這個指令查詢因為修改過而不能git push的檔案。

#### 再來我們有可能是去下載一個檔案來做開發。

`git pull`push是上推檔案到遠端的話pull就是從遠端下拉檔案的意思囉，可以把別人的專案更新到最新狀態。
`git merge (branch name)`在別的分支開發玩的功能要合併到比較主要的分支就下這個指令，要注意如果有衝突(conflict)的話Git會提示你，請小心解決，個人會比較推薦使用Github上的功能更有可讀性。


## Git和Github是個博大精深的世界，要熟悉真的要自己找機會去熟悉，參考方向和資料來源如下：
[Tsung's Blog](https://blog.longwin.com.tw/2009/05/git-learn-initial-command-2009/)
[外國人詢問branch的問題](http://stackoverflow.com/questions/10312521/how-to-fetch-all-git-branches)

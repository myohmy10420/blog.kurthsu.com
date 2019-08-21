---
title: '[Github]SSH key 自動驗證到自己的Github上'
date: 2018-04-27 11:44:00
categories:
 - [Github]
tags:
 - Github
 - SSH
---
Github在做git push或者要登入雲端機的時候都需要打密碼，我們可以利用SSH key來直接操作或登入。

原理是非對稱加密中有公鑰(public key)和密鑰(private key)：
> 透過公鑰加密的密文， 只有密鑰能夠解開
>  透過密鑰加密的密文， 只有公鑰能夠解開

如果本機端沒有公鑰和密鑰請執行：
`$ ssh-keygen -t rsa`
即會產生
公鑰`~/.ssh/id_rsa.pub`
密鑰`~/.ssh/id_rsa`
這兩個檔案
`密鑰請一定要保管好！`

再來登入自己的機器
`$ ssh kurt@xxx.xxx.xxx.xx`
`$ mkdir ~/.ssh`
`$ touch ~/.ssh/authorized_keys`

把剛剛本機端的印出來
`$ cat ~/.ssh/id_rsa.pub`

在雲端上貼入剛剛`~/.ssh/authorized_keys`這個檔案

`$ chmod 700 ~/.ssh`
`$ chmod 644 ~/.ssh/authorized_keys`

應該就大功告成了！
如果不知道chmod請參考我另一篇文章[Linux 權限管理](http://myohmy10420-blog.logdown.com/posts/7427437)

## 不允許遠端登入
---
假設不允許root使用遠端登入：

用root權限編輯 /etc/ssh/sshd_config
修改`PermitRootLogin no`這樣就不允許root選端登入。

如果你有設好用公鑰登入，也可以修改`PasswordAuthentication no`，這樣就不能用輸入密碼的方式登入。這樣服務器會安全的多，因為駭客就不能猜密碼了。只是說如果你不是用自己的電腦想要登入服務器，沒有公鑰檔案的話就沒有辦法登入了。

最後
`sudo service ssh restart`
就會重啟 SSH 套用設定。

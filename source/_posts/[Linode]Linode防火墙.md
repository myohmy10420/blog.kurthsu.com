---
title: '[Linode]Linode防火墙'
date: 2018-04-29 09:19:00
categories:
 - [Linode]
 - [Linux]
tags:
 - Linux
 - Linode
 - 防火牆
---
Linode 並沒有內建防火牆功能，可以用 ufw 來設定 Linux 要開放哪些 Port。

以下是開放 Port 3000, 80, 443 的指令：
`$ sudo apt-get install ufw`
`$ sudo ufw default deny`
`$ sudo ufw allow 3000`
`$ sudo ufw allow 80`
`$ sudo ufw allow 443`
`$ sudo ufw enable` 啟用防火牆
`$ sudo ufw status` 顯示目前狀態

如果發現網站被特定IP攻擊，也可以用 ufw 來丟棄特定來源的數據包：
`$ sudo ufw insert 1 deny from <要ban掉的IP>`
`$ sudo ufw reload`

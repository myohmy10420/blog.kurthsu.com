---
title: '[Devops]使用ansible-vault加解密'
date: 2020-02-29 17:08:29
categories:
 - [Devops]
tags:
 - Devops
 - ansible-vault
---

ansible是一套基於Python寫的自動部署工具(指令為ansible-playbook), 主要是用來管理機台環境, 這篇主要紀錄簡單使用ansible-vault加解密

## 安裝

`$ brew install python3`
`$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py`
`$ python3 get-pip.py`
`$ pip3 -V`
以上確認安裝pip3, 是一套python套件管理工具
`$ pip3 install ansible`
`$ ansible-vault --version`

## 使用

`$ ansible-vault encrypt file_name`
然後輸入兩次相同的密碼就可以看到該檔案被加密
`$ ansible-vault encrypt file_name1 file_name2 file_name3`
可以同時加密一堆檔案

`$ ansible-vault decrypt file_name`
然後手動輸入密碼即可解密該檔案, 但這會直接把檔案變成明文, 所以解密使用玩要非常小心不要外洩
`$ ansible-vault decrypt file_name1 file_name2 file_name3`
當然也可以同時解密一堆檔案

`$ ansible-vault view file_name`
這不會把檔案變成明文, 只會less明文
這樣就不用每次解密完就得再加密回去, 除了很麻煩之外也怕設密碼的時候有問題, 例如只是要解密pem檔複製貼上而已就非常方便

`$ ansible-vault decrypt file_name --output new_file`
把解密的東西產生新的檔案, 不會把原本加密的檔案變明文

`$ ansible-vault view file_name --vault-id pwd_file`
也可以建立存放密碼的檔案, 然後用該檔案去解密

`$ ansible-vault rekey file_name`
則可以更改密碼

## 參考文章
[ansible 官網](https://docs.ansible.com/ansible/latest/user_guide/vault.html)
[現代 IT 人一定要知道的 Ansible 自動化組態技巧](https://chusiang.gitbooks.io/automate-with-ansible/content/30.how-to-manage-sensitive-data-with-vault.html)

---
title: '[element-ui]form 應用 & 命名小地雷'
date: 2017-09-14 09:55:00
categories:
 - [element-ui]
tags:
 - element-ui
 - form
 - naming
---
工作上的應用：
1. 錯誤訊息動態產生的空間
2. 驗證自訂規則
3. 確認密碼的表格
4. 命名小地雷

## 錯誤訊息動態產生的空間
在element ui的input裡他是有預留錯誤訊息的空間的，但這讓表單看起來有點空洞，因為輸入欄與輸入欄間距有點大，有點浪費輸入空間，用開發者工具指向出現的錯誤訊息，發現其實很好改動：
``` css
.el-form-item__error {
	position: relative;
}
```
這樣就是動態產生的空間了。

## 驗證自訂規則
官方文件有寫很基本的寫法，稍微玩過應該就知道怎麼寫，但網路上有很多人使用最精簡的寫法，用正則表示式做驗證，直接看簡單的範例：

4~15碼開頭需要用英文的帳號驗證

``` javascript
someRules: {
	account: [
		{ required: true, message: ruleErrorMes.inputAccount, trigger: 'blur' },
		{ validator(r,v,b){(/^[a-zA-z]\w{3,15}$/).test(v)?b():b(new Error( errorMessage ))} }
	]
}
```
如果單純用正則判斷的話只要開正則的部分就好，剩下就是帶入自己要的errorMessage，只需要改兩個地方。

## 確認密碼的表格
其實只是簡單的判定兩個變數是不是相等，但我不知道怎麼在rule的function裡面找到我要的變數，所以我跳脫出他的from改用單純的input硬刻，時間上允許的話再回頭研究吧！

## 命名小地雷
先看簡單又正確的範例
``` html
<el-form :model="someModel" :rules="someModelRules" ref="someForm">
	<el-form-item :label="'account'" prop="account">
		<el-input type="text"
			v-model="someModel.account"
			:placeholder="'input here'">
		</el-input>
	</el-form-item>
</el-form>
```
在js
``` javascript
someModel: {
    account: ''
}
someModelRules: {
    account: [
		{ required: true, message: ruleErrorMes.inputAccount, trigger: 'blur' },
		{ validator(r,v,b){(/^[a-zA-z]\w{3,15}$/).test(v)?b():b(new Error( errorMessage ))} }
	]
}
```
注意！！！！！！！！
`Model和Rules兩個物件裡面要驗證的名稱一定要互相對應`，不然運作起來會整個一直狂驗證不過！！！

也就是說要驗證model的account，使用的規則名稱也必須是account，這實在很玄，他們應該是不相干的東西但不知道為什麼只要我用不一樣的名稱驗證起來就會一直不過，只有兩個一樣運作才正常。


記錄一下工作上遇到的東西，可能是我哪個環節有問題，歡迎指教～

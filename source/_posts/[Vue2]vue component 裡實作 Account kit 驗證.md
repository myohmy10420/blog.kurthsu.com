---
title: '[Vue2]vue component 裡實作 Account kit 驗證'
date: 2017-09-07 06:31:00
categories:
 - [Vue2]
tags:
 - Vue2
 - component
 - Account kit
---
Account kit可以做到手機或信箱驗證，其實實作起來真的很簡單，但是在Vue的component裡還是要注意一些小地方，這邊把我遇到的問題全部記錄下來。

不過這邊不贅述如何申請開發者帳號和申請應用程式app，他文件上已經夠清楚。

## 載入後的初始化

首先在html上`一定要在header裡就載入sdk，並且在body後端做初始化動作`：
``` html
<!DOCTYPE HTML>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
	<title>Account kit</title>
	<script type="text/javascript" src="https://sdk.accountkit.com/en_US/sdk.js"></script>
</head>
<body>
	<div id="header" ref="header" v-cloak></div>
	<div id="app">
		<router-view></router-view>
	</div>
	<script>
		AccountKit_OnInteractive = function(){
			console.log('Initing signup funciton');
			AccountKit.init(
				{
					appId:"{{FACEBOOK_APP_ID}}",
					state:"{{csrf}}",
					version:"{{ACCOUNT_KIT_API_VERSION}}",
					fbAppEventsEnabled:true,
					Redirect:"{{REDIRECT_URL}}"
				}
			);
			console.log('Inited signup funciton');//為什麼下一個console在這後面會提到
		};
	</script>
	<script type="text/javascript" src="/dist/build.js"></script>
</body>
</html>
```
你會發現文件還有其他三個function，但那個是我要放在component的methods裡面的，在這邊也不贅述build.js裡面有什麼東西，直接寫component.vue的檔案：

P.S:這邊只取簡訊驗證所以不寫emailLogin的東西，兩個邏輯是一樣的。
``` vue
<template>
	<div>
		<input v-model="countryCode" />
		<input placeholder="phone number" v-model="phoneNumber"/>
		<button @click="smsLogin">Login via SMS</button>
	</div>
</template>

<script>
import Vue from 'vue'
export default {
	name: 'home',
	data: function () {
		return {
			countryCode: '+886',
			phoneNumber: ''
		}
	},
	methods: {
		smsLogin: function () {
			var countryCode = document.getElementById("country_code").value;
			var phoneNumber = document.getElementById("phone_number").value;
			AccountKit.login(
				'PHONE',
				{countryCode: countryCode, phoneNumber: phoneNumber}, // will use default values if not specified
				this.loginCallback
			);
		},
		loginCallback: function (response) {
			if (response.status === "PARTIALLY_AUTHENTICATED") {
				var code = response.code;
				var csrf = response.state;
				// Send code to server to exchange for access token
			} else if (response.status === "NOT_AUTHENTICATED") {
				// handle authentication failure
			} else if (response.status === "BAD_PARAMS") {
				// handle bad parameters
			}
		}
	}
}
</script>
<style lang="scss">
//some...
</style>
```

正常來講這樣就可以啟動Acoount kit的功能囉！

## 這邊紀錄一下一些問題以及我的作法：

#### 問題面
account kit 方面

1. 在html用script載入sdk.js的檔案之後他會執行裡面的function產生AccountKit_OnInteractive這個變數並且watch他，所以在`我們下AccountKit_OnInteractive = function () { AccountKit.init() }的時候他自己會執行 AccountKit.init()一次，而且他要抓到正確的AccountKit.init參數`。
2. sdk.js載入之後`AccountKit裡面有init和login這兩個function`，`要使用login之前一定要執行過init`。
3. 執行過AccountKit.init之後再執行一次會報錯。
4. AccountKit.init的資訊似乎會在暫存裡面，所以刷新頁面如果出現`donotlinktosdkdirectly的錯誤代表在抓到真正這次的sdk.js之前已經過早執行AccountKit.init`。

vue 方面

5. conponent的methods裡面雖然可以呼叫windows的東西但從外面無法呼叫這個component的methods(或許真的有辦法)，這會造成如果account kit function全都寫在windows 的話會造成完成後的function loginCallback執行上的困難。
6. 雖然可以在component裡面呼叫到window的AccountKit_OnInteractive，但在`vue裡面直接下AccountKit.login()他卻找不到正確的AccountKit.init的參數`，我在想或許AccountKit在每個component裡面或許都是自己獨立的。

#### 解決方法
問題1,2如何知道sdk.js完整抓下來並且正確的AccountKit.init()一次過，就像我範例中的html上下一段`console.log('Inited signup funciton');`即可清楚辨別，也可以避免問題3,4的問題。

問題5,6因為我們在html的window init了我們的AccountKit.init，為了讓AccountKit.login抓到window裡正確的參數，指定執行window.AccountKit.login即可。


可能是對很多東西還不是很熟，或許有更棒的做法，但我收尋不太到真正解到我痛點的文章，至少目前這個做法是很ok的，歡迎大家指點。

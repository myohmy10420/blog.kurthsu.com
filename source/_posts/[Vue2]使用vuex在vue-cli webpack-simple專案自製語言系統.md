---
title: '[Vue2]使用vuex在vue-cli webpack-simple專案自製語言系統'
date: 2017-08-24 07:34:00
categories:
 - [Vue2, vuex]
tags:
 - Vue2
 - vuex
 - vue-cli
 - webpack-simple
---
我的目的是希望做一個單獨的js檔案管理語言包，使所有的component都可以import使用

## 專案裡所需的東西
1. vuex
2. .json檔案做成的語言包
3. 不要懷疑原來就是這麼精簡的心情


## 開始製作語言系統
我們先來製作兩個語言包吧！
首先創造兩個.json檔案分別為：
tw.json
en.json

``` json tw.json
{
  language: '中文'
}
```
``` json en.json
{
  language: 'English'
}
```

再來創造一個專門管理語言的js檔案，我命名為config：
```javascript config.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

var config = new Vuex.Store({
	state: {
		language: 'tw',
		translations: {}
	},
	mutations: {
		getTranslations () {
			if ( state.language === 'tw' ) {
				$.ajax({
					url:'./tw.json',
					type: 'get',
					async: false,//設定同步讓語言包一定要先取到才export出去
					success: function (response) {
						state.translations = response;
						state.language === 'en'
					}
				});
			} else {
				$.ajax({
					url: './en.json',
					type: 'get',
					async: false,
					success: function (response) {
						state.translations = response;
						state.language === 'tw'
					}
				});
			}
		}
	}
});
```

之後開始創造html：
P.S:這裡用最兩光的按鈕直接切換語系，正常來講是下拉式選單之類的才最適合。
```html index.html
<center id="showTranslations" v-cloak>
  <button @click="changeLanguage">change</button>
  現在語系:{{ translations.language }}
</center>
<div id="component"></div>
```

開始撰寫main.js
```javascript main.js
import Vue from 'vue'
import Vuex from 'vuex'
import config from './config.js'

import myComponent from './component.vue'

Vue.use(Vuex)

//把從.vue檔案import的component實作出來
new Vue({
	el: '#component',
	render: h => h(myComponent)
})

//主要的東西也可以直接寫在此js裡面
new Vue({
	el: '#showTranslations',
	data: function () {
		return {
			someData: ''
		}
	},
	methods: {
		changeLanguage: function () {
			config.commit('getTranslations');
			//要呼叫vuex的function要使用commit
		}
	},
	computed: {
		translation() {
			return config.state.translations;
			//只用這種方法去讀取語言包！
		}
	}
})
```

開始撰寫component.vue
``` vue component.vue
<template>
	<center>
		component語系:{{ translations.language }}
	</center>
</template>

<script>
import Vue from 'vue'
import Vuex from 'vuex'

import config from './config.js'
//每個component都要引進語言包！
Vue.use(Vuex)
export default {
	data() {
		return {
			someData: ''
		}
	},
	computed: {
		translation() {
			return config.state.translations;
		}
	}
}
</script>
```

這樣就可以簡單模擬一個專案有一隻js檔案是使用vuex達到讓每個component運用裡面的資料做很多初始化的事情囉！

執得注意的地方：
config的ajax一定要使用同步執行完整才export出去(async: false)，promise，axios等等都沒辦法做到。

這個方法有個比較大的缺點是json擋就得很土炮的自己一個一個字打上去囉！

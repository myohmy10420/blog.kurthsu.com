---
title: '[Vue2]vue form 搭配 vue-axios基本使用'
date: 2020-05-22 21:52:26
categories:
 - [Vue2]
tags:
 - Vue2
---

記錄一下自己如何管理 Vue file 的表單, 並且用 vue-axios 做 post, 不過這是野生(就是我自己想的)的方式, 請參考即可

先列出幾個主題可以直接收尋:
1. 包裝 postData
2. 如何處理 form errors
3. 注意 axios callback 裡如何使用 i18n

下面是一個簡單的完整示範:
```vue
<template>
  <form
    @submit="postForm"
    action="/somewhere"
    method="post"
  >
    <div v-for="(value, key) in postData" :key="key" class="field">
      <label class="label">{{ $t(key) }}</label>
      <input
        :name="postData[key]"
        v-model="postData[key]"
        :class="{ 'is-danger': errors[key] }"
      >
      <p v-if="errors[key]">
        {{ $t('errors.' + errors[key]) }}
      </p>
    </div>
  </form>
</template>

<script>
export default {
  data: function () {
    return {
      postData: {
        email: '',
        password: '',
        password_confirmation: ''
      },
      errors: {
        email: '',
        password: '',
        password_confirmation: ''
      }
    }
  },
  methods: {
    postForm: function (e) {
      e.preventDefault()

      this.validForm()
      if (!this.hasErros()) {
        this.axios.post('/somewhere', this.postData)
          .then(response => {
            let { status, data } = response

            if (status === 200) {
              alert(this.$root.$t(data))
            } else {
              alert(this.$root.$t(data))
            }
          })
          .catch(error => {
            let { status, data } = error.response

            if (status === 400) {
              alert(this.$root.$t(data))
            } else {
              alert(this.$root.$t(data))
            }
          }
      }
    },
    validForm: function () {
      // Do validations...
    },
    hasErros: function () {
      let bool = false
      Object.keys(this.errors).forEach(key => {
        if (!!this.errors[key]) { bool = true }
      })
      return bool
    }
  }
}
<script>
```

## 1. 包裝 postData

像在 data 做以下整理:
```vue
data: function () {
  return {
    postData: {
      email: '',
      password: '',
      password_confirmation: ''
    }
  }
}
```

第一個好處是可以直接用一行做 post
`this.axios.post('/somewhere', this.postData)`

第二個好處是可以在 template v-for 直接把所有的 input 印出來
```vue
<div v-for="(value, key) in postData" :key="key" class="field">
  <label class="label">{{ $t(key) }}</label>
  <input
    :name="postData[key]"
    v-model="postData[key]"
    :class="{ 'is-danger': errors[key] }"
  >
</div>
```

如果要動態新增欄位則需要更新
```vue
this.$set(this.postData, key, value)
```

## 2. 如何處理 form errors

可以注意到我的 data 除了包含 postData 還有 errors, 不過這等於重複寫了一次
```vue
data: function () {
  return {
    postData: {
      email: '',
      password: '',
      password_confirmation: ''
    },
    errors: {
      email: '',
      password: '',
      password_confirmation: ''
    }
  }
}
```

可以注意到我在 v-for 製造 input tags 的時候也都把錯誤訊息的 DOM 用 v-if 做了出來

```
<div v-for="(value, key) in postData" :key="key" class="field">
  <input ..../>
  <p v-if="errors[key]">
    {{ $t('errors.' + errors[key]) }}
  </p>
</div>
```

這樣也方便直接規劃 i18n, 例如 api 回傳的 error 訊息為:
```JSON
{
  email: 'invalid_email'
}
```

如果用 rails 專案自打字給規劃, 甚至可以直接整個 errors 原封不動的接過來更新 vue datas
```javascript
this.axios.post('/somewhere', this.postData)
  .then(response => {
    this.closeModal()
  })
  .catch(error => {
    let { status, data } = error.response
    Object.keys(data).forEach(key => {
      this.errors.user[key] = data[key].join('\n')
    })
  })
```

我只要設計 locale 如以下就吃得到
```javascript
export default {
  errors: {
    invalid_email: '無效的 E-mail'
  }
}
```

最後可以寫一個 method 去檢查 errors 有沒有任何錯誤回傳一個 boolean 值
```vue
hasErros: function () {
  let bool = false
  Object.keys(this.errors).forEach(key => {
    if (!!this.errors[key]) { bool = true }
  })
  return bool
}
```

不過有幾個缺點:
1. errors 基本上就是重新再寫一次 postData 的東西
2. errors 不能先只給空 object {} 後來再慢慢新增, vue 會偵測不到
> 也就是如果像下面這樣設計後來執行 `this.errors.email = 'invalidatedEmail'` vue 是吃不到的, 畫面不會更新
```vue
data: function () {
  return {
    postData: {
      email: '',
      password: '',
      password_confirmation: ''
    },
    errors: {}
  }
}
```

3. 如果寫 v-show 的話會因為一開始沒有錯誤而噴 error 找不到`$t('errors.')`的翻譯
4. 動態新增或刪減 postData 可能都要對 errors 重複做一次一樣的操作

這是野生的方法XD
不過我目前的小專案是覺得這些缺點還沒有關係, 大專案如果有有經驗的前端應該會有更好的規劃

## 3. 注意 axios callback 裡如何使用 i18n

注意到 axios 的 callback 的 scope, 想用`$t('trans.xxx')`執行 i18n 的話要改成 `this.$root.$t('trans.xxx')`

參考來源:
首先感謝大學同學(Hydra)[https://github.com/hydragg]大大的一些指導
(Hydra動態新增欄位的示範)[https://jsbin.com/luzosafate/1/edit?html,js,console,output]
(How to fix "this is undefined" in Vue -> 可以了解一下什麼時候該不該用 arrow function)[https://michaelnthiessen.com/this-is-undefined/]

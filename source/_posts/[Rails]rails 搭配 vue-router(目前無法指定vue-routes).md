---
title: '[Rails]rails 搭配 vue-router(目前無法指定vue-routes)'
date: 2018-05-12 19:52:00
categories:
 - [Rails, Vue]
tags:
 - Rails
 - Vue
 - vue-router
---
參考文件：
[webpacker](https://github.com/rails/webpacker)

Rails版本5.1以上可以直接創造帶有webpack的新專案
`$ rails new rails-vue-todolist --skip-turbolinks --skip-spring --webpack=vue`
`$ cd rails-vue-todolist`

先快速建立todolist
`$ rails g scaffold Todolist item:string`
`$ bundle install`

設定router
``` ruby routes.rb
root "todolists#index"
```

測試有沒有成功
``` ruby app/views/layout/application.html.erb
<%= javascript_pack_tag 'hello_vue' %>
```

更新todolist/index
``` ruby app/views/todolists/index.html.erb
<div id="hello"></div>
...
```

`$ rails s`
到首頁刷新頁面應該就會有了，如果有出現：
`You may need an appropriate loader to handle this file type.`
可以嘗試下下面三個指令
`$ bundle update webpacker`
`$ yarn upgrade @rails/webpacker`
`$ bundle exec rails webpacker:install:vue`

大多會是版本上的問題，如果直接用rails5.1以上應該沒有問題，如果是用其他版本或者是專案升級情況可能要再去查詢下[官方log文件](https://github.com/rails/webpacker/blob/master/CHANGELOG.md#breaking-changes)。

再來如果玩過Vue.js的人有一種開發模式是把一個id="app"當作入口，所以我們會有一個主要的`main.js`和主要生成vue的`app.vue`，此時`app/javascript/packs/application.js可以把它當作main.js`，再來怎麼規劃檔案就都可以了。

我們循著上面的作法先改rails view剛剛的application.html.erb的tag
``` ruby app/views/layout/application.html.erb
# 把原本的 <%= javascript_pack_tag 'hello_vue' %> 替換如下

<%= javascript_pack_tag 'application' %>
```

修改index入口點只留下一行當作vue的入口就好
``` ruby app/views/todolists/index.html.erb
<div id="app"></div>
```

修改packs/application.js
``` javascript app/javascript/packs/application.js
import Vue from 'vue'
import App from '../app.vue'

document.addEventListener('DOMContentLoaded', () => {
  const app = new Vue({
    el: "#app",
    render: h => h(App)
  })
})
```
現在再重新刷新一次頁面如果有render出Hello Vue!恭喜已經設定好所有一切了！

## 初創index
---
接下來我們先直接在app.vue裡面創造假資料做出index畫面：
``` javascript app/javascript/app.vue
<template>
  <div>
    <h1>Todo Lists</h1>
    <table>
      <thead>
        <tr>
          <th>#</th>
          <th>Item</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="todo in list" >
          <td>{{ todo.id }}</td>
          <td>{{ todo.item }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      list: [
        { id: 1, item: "Foo" },
        { id: 2, item: "Bar" }
      ]
    }
  }
}
</script>
```

應該會list出兩個item出來，但我們要去拿rails裡todolists#index的資料，首先我們在rails console新增兩筆：
`$ rails c`
`$ Todolist.create(item: "Test1")`
`$ Todolist.create(item: "Test2")`
`$ exit`

然後如何從vue的methods裡面拿取資料，可以用[vue-resource](https://github.com/pagekit/vue-resource)這個套件。
`$ yarn add vue-resource`

然後import在app/javascript/application.js裡：
``` javascript app/javascript/application.js
import VueResource from 'vue-resource'
Vue.use(VueResource);
```

現在修改app.vue
``` javascript app/javascript/app.vue
<template>
  <div>
    <h1>Todo Lists</h1>
    <table>
      <thead>
        <tr>
          <th>#</th>
          <th>Item</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="todo in list" >
          <td>{{ todo.id }}</td>
          <td>{{ todo.item }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      list: []
    }
  },

  created: function() {
   this.fetchTodoLists();
  },

  methods: {
    fetchTodoLists: function() {
       const resource = this.$resource('/todolists.json');
       resource.get()
        .then(response => {
           this.list = response.data
        });
    }
  }
}
</script>
```
應該會出現剛剛在console創的兩筆資料。

## component裡面寫css
---
在[webpacker官方文件](https://github.com/rails/webpacker)裡面有規劃資料夾，雖然敘述說會有放js css images的地方但我初創的時候只有放js的資料夾，他的架構如下：
app/javascript:
  ├── packs:
  │   # only webpack entry files here
  │   └── application.js
  └── src:
  │   └── application.css
  └── images:
      └── logo.svg

照著他的架構創
`$ mkdir app/javascript/src`
`$ touch app/javascript/src/application.css`

然後在原本rails的application.html.erb加上連結
``` html app/views/layouts/application.html.erb
<%= stylesheet_pack_tag 'application' %>
```
就可以在每個.vue檔的component裡面寫css了，例如：
``` javascript component.vue
<style lang="scss">
  .link {
    color: blue;
    cursor: pointer;
  }
</style>
```

## 引入vue-router
---
再來跟全端開發一樣，rails的gem歸rails的Gemfile，而webpack的歸package.json(Vue也是在這裡面，只不過rails5.1連著專案一起創webpack的話已經內建Vue了！)。

現在安裝vue-router
`$ yarn add vue-router`

先把router寫在一個js裡面：
`$ touch app/javascript/packs/router.js`
建立routes
``` javascript app/javascript/packs/router.js
import Vue from 'vue'

import VueRouter from 'vue-router'
Vue.use(VueRouter)

import todolists_index from './../views/todolist/index.vue'

const router = new VueRouter({
  // 使用 HTML 5 模式
  mode: 'history',
  routes: [
    { path: '/todolists', component: todolists_index },
    { path: '/', redirect: '/todolists' }
  ]
})

export default router
```
`檔名或變數不要取routes避免產生match error`

修改packs/application.js把
``` javascript app/javascript/packs/application.js
import Vue from 'vue'
import App from '../app.vue'

import VueResource from 'vue-resource'
Vue.use(VueResource);

import VueRouter from 'vue-router'
Vue.use(VueRouter)
import router from './router.js'

document.addEventListener('DOMContentLoaded', () => {
  const app = new Vue({
    el: "#app",
    router,
    render: h => h(App)
  })
})
```

現在創造專給vue的view的資料夾
`$ mkdir app/javascript/views`
再給todolist一個歸類
`$ mkdir app/javascript/views/todolist`
最後先做index檔案，根本來的app.vue一模一樣
`$ cp app/javascript/app.vue app/javascript/views/todolist/index.vue`

最後app.vue簡化如下：
``` javascript app/javascript/app.vue
<template>
  <div>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>
```
就成功import vue-router了！這樣也算是做出前後端分離的概念了，rails去吐api，而vue就專門做前端的東西吧！

## 但目前遇到的問題是重整頁面會去讀取rilas routes，要怎麼設定待研究

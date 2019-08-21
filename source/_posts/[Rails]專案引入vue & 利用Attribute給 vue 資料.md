---
title: '[Rails]專案引入vue & 利用Attribute給 vue 資料'
date: 2018-05-14 09:20:00
categories:
 - [Rails, Vue]
tags:
 - Rails
 - Vue
 - attribute
---
我的開發環境：
Rails 5.1以上

## 引入webpacker和vue
---
先安裝[web packer](https://github.com/rails/webpacker)
``` ruby Gemfile
gem 'webpacker', '~> 3.5'
```
`$ bundle install`
`$ bundle exec rails webpacker:install`

可以查看webpacker帶有什麼指令：
`$ rails webpacker`

比起npm我比較喜歡用yarn：
`$ bundle exec webpacker:yarn_install`
`$ yarn install`

有個指令可以直接安裝vue：
`$ bundle exec webpacker:install:vue`

## 利用Attribute給 vue 資料
---
先在contoller設定變數：
``` ruby products_controller.rb
def index
  @products = Product.all
end
```

再到view裡面把資料帶入DOM：
``` html index.html.erb
<% props = {
  products: @products
  }.to_json
%>

<div id="app" data="<%= props %>"></div>
```

接下來到vue的檔案application：
``` javascript app/javascript/packs/application.js
import Vue from 'vue'
import App from '../app.vue'

document.addEventListener('DOMContentLoaded', () => {
  const el = "#app"

  const props = JSON.parse($("#app")[0].getAttribute('data'))
  const app = new Vue({
    el: el,
    render: h => h(App, { props })
  })
})
```

最後到vue 的component：
``` javascript app/javascript/app.vue
<script>
export default {
  props: ["products"],
  data: function () {
    return {
      message: "Hello Vue!"
    }
  },
  mounted: function () {
    console.log("products", products)
  }
}
</script>
```
mounted是vue生成完之後會執行的動做，可以參考[vue lifecycle](https://vuejs.org/v2/guide/instance.html)。
如果有console出東西就ok嘍！！

這個方式的優點十分明顯，可以省去製作API和串接的麻煩，但就是比較不彈性，真正要做到完全的前後端分離建議還是分成兩個專案，Rails寫API而Vue去做前端實現。

參考文章：
[Passing Props to Vue in a Rails View](https://medium.com/@devanflaherty/passing-props-to-vue-in-a-rails-view-56e287be9c2d)

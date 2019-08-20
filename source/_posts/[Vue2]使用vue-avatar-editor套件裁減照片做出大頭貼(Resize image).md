---
title: '[Vue2]使用vue-avatar-editor套件裁減照片做出大頭貼(Resize image)'
date: 2017-09-15 07:12:00
categories:
 - [Vue2]
tags:
 - Vue2
---
如果對於canvas熟悉的人不一定要使用這個套件，畢竟我是因為這個套件才認識了幾本的canvas用法，畢竟他的文檔沒有更多後續的資訊，[vue-avatar-editor的Github](https://github.com/two20/vue-avatar-editor)。

## 使用前須知：
1. 不用npm install這個套件只需自行創造這兩個.vue檔案import到使用的地方就好。
2. 這個團隊似乎沒在維護這個Github了，有個簡單的錯誤`tag <canvas> has no matching end tag.`也沒修，[自行到VueAvatar.vue](https://github.com/two20/vue-avatar-editor/tree/master/src/components)這個檔案的template裡面幫他加上去`</canvas>`即可。

## 如何簡單使用vue-avatar-editor：
他的屬性真的不多，文件上就可以輕鬆掌握，裁減之後會觸發他預設的saveClicked()，他會給我們一個變數名稱是一個canvas的tag，然後呢？然後他的文件就到此為止了XD。

但其實這就是出圖的東西囉！！我們需要把這張圖片變成程式碼格式，其中一種格式只要下`var pictureURL = img.toDataURL("image/png");`再把他console.log出來會發現是一堆人看不懂的字串，但是電腦是看得懂的！

接著只要創造一個img的tag去裝他就行了！只要下這行`$('.picture').attr('src', imgURL);`就可以看到才剪出來的圖片了！

直接看範例吧：
p.s:我有用到jQuery喲！
先看html
```html
<div id="app">
  <vue-avatar
    :width=400
    :height=400
    ref="vueavatar"
    @vue-avatar-editor:image-ready="onImageReady"
    image="https://vuejs.org/images/logo.png"
  >
  </vue-avatar>
  <br>
  <vue-avatar-scale
    ref="vueavatarscale"
    @vue-avatar-editor-scale:change-scale="onChangeScale"
    :width=250
    :min=1
    :max=3
    :step=0.02
  >
  </vue-avatar-scale>
  <br>
  <img src="" id="img-1">
  <button v-on:click="saveClicked">Click</button>
  <img src="#" class="picture">
</div>
```
看看JS
```javascript
import Vue from 'vue'
import VueAvatar from './components/VueAvatar.vue'
import VueAvatarScale from './components/VueAvatarScale.vue'

let vm = new Vue({
  el: '#app',
  components: {
    VueAvatar,
    VueAvatarScale
  },
  methods:{
    onChangeScale (scale) {
      this.$refs.vueavatar.changeScale(scale)
    },
    saveClicked(){
      var img = this.$refs.vueavatar.getImageScaled()
      // use img
      var pictureURL = img.toDataURL("image/png");
      $('.picture').attr('src', imgURL);
    },
    onImageReady(scale){
      this.$refs.vueavatarscale.setScale(scale)
    }
  }
})
```
沒意外的話就可以把img做出來了

## Canvas與Image可以互相轉換：
[這個網站](http://sunbox.github.io/image-to-canvas-to-image/)有最簡單的function：
```javascript
//用<img>製作一個新的<canvas>
// Converts image to canvas; returns new canvas element
function convertImageToCanvas(image) {
	var canvas = document.createElement("canvas");
	canvas.width = image.width;
	canvas.height = image.height;
	canvas.getContext("2d").drawImage(image, 0, 0);

	return canvas;
}
//用<canvas>製作一個新的<img>
// Converts canvas to an image
function convertCanvasToImage(canvas) {
	var image = new Image();
	image.src = canvas.toDataURL("image/png");
	return image;
}
```

單純的互相轉換其實沒有這麼複雜，網路上有一堆文章幾乎都寫得大同小異，最常看到應該是使用filereader，filereader可以處理更多種類的檔案還有狀態，但我這裡不需要這麼複雜的操作。

只是因為需要做裁減的動作的話必須熟悉Canvas，但既然我們都沒有這麼多時間的話vue-avatar-editor確實幫我們做好了，所以vue-avatar-editor搭配這個網站的簡述就可以簡易做出大頭貼囉！

## 參考文章
[vue-avatar-editor](https://github.com/two20/vue-avatar-editor)
[Convert an Image to Canvas with JavaScript](http://sunbox.github.io/image-to-canvas-to-image/)

---
title: '[Rails]form_for preview image with javascript'
date: 2018-05-06 06:33:00
categories:
 - [Rails]
 - [JavaScript]
tags:
 - Rails
 - form_for
 - JavaScript
---
原理是利用Javascript的FileReader製作一個DOM，再利用CSS把舊的圖片藏起來。

``` html view.html.erb
<%= form_for @profile do |f| %>
  <div class="form-group" style="width: 300px;">
    <%= f.file_field :image, required: true, accept:'image/png,image/jpeg', id: :image %>
  </div>

  <div id="image_preview"></div>

  <% if @profile.image.present? %>
    <%= image_tag @profile.image.url(:medium), :id => "old_image" %>
  <% end %>

  <div class="form-group" style="width: 300px;">
    <%= f.submit "上傳", class: "btn btn-lg btn-primary" %>
  </div>
<% end %>

<script type="text/javascript">
$(function() {
  $('#image').on('change', function(event) {
    let files = event.target.files;
    let image = files[0]
    let reader = new FileReader();
    reader.onload = function(file) {
      let img = new Image(400, 600);
      img.src = file.target.result;
      $('#image_preview').html(img);
      $('#old_image').css("display", "none");
    }
    reader.readAsDataURL(image);
  });
});
</script>
```
1.上傳圖片後會觸發change這個function
2.event.target可以拿到id="image"這個tag
3.event.target.file[0]則拿到這個檔案的本體File 格式的物件
4.創造一個FileReader
5.註冊一個FileReader的onload事件，只要拿FileReader做事情就會觸發onload事件
6.用readAsDataURL讀取File本體，完成後 result 將以 data: URL 格式（base64 編碼）的字串來表示讀入的資料內容
7.觸發的onload變數file是剛剛readAsDataURL讀取出來的東西
8.創造一個Image DOM 尺寸是400px * 600px
9.file.target.result是剛剛readAsDataURL讀取File本體回傳的data: URL 格式
10.利用html()把它放進 $('#image_preview')裡面
11.css把舊的$('#old_image')藏起來

<%= image_tag @profile.image.url(:medium), :id => "old_image" %>是用[paperclip](https://github.com/thoughtbot/paperclip)這個套件做的

小補充：
Blob是一個檔案（原始資料）的不可變物件，剛剛的File 格式的物件其實也是一種特殊的Blob，所以也可以用在任何接受 Blob 物件的地方，例如FileReader。

參考文件：
[How to preview uploaded image instantly with paperclip in ruby on rails](https://stackoverflow.com/questions/25474329/how-to-preview-uploaded-image-instantly-with-paperclip-in-ruby-on-rails)
[MDN FileReader](https://developer.mozilla.org/zh-TW/docs/Web/API/FileReader)
[MDN Blob](https://developer.mozilla.org/zh-TW/docs/Web/API/Blob)
[MDN Image()](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLImageElement/Image)

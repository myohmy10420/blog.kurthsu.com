---
title: '[Rails]Bootstrap制作漂亮flash'
date: 2018-05-04 08:51:00
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - Bootstrap
---
Rails有個內建的Flash還蠻好用的，但就是有點簡略，一開始還真不知道怎麼改他的畫面，先用簡單的例子理解它：

隨便創作一個method然後byebug：
``` ruby controller.rb
def test
  flash[:alert] = "Alert!"
  byebug
end
```
此時在終端機下的byebug之下查看
`$ flash`
會出現
\<Set: {}>, @flashes={"alert"=>"Alert!"}, @now=nil>
就利用這個特性來客製一下flash的畫面吧！

在Rails`最通用的三個flash分別是alert, notice, warning`，現在我們看[bootstrap的alert](https://getbootstrap.com/docs/3.3/components/#alerts)，然後挑選自己喜歡的顏色後，準備一個module給flash：
`$ touch app/helpers/flashes_helper.rb`
建立如下：
``` ruby flashes_helper.rb
module FlashesHelper
  FLASH_CLASSES = { alert: "danger", notice: "success", warning: "warning"}.freeze

  def flash_class(key)
    FLASH_CLASSES.fetch key.to_sym
  end

  def user_facing_flashes
    flash.to_hash.slice "alert", "notice","warning"
  end
end

```
現在還看不懂吧！我們先建立view：
``` ruby view.html.erb
<% if flash.any? %>
  <% user_facing_flashes.each do |key, value| %>
    <div class="alert alert-dismissable alert-<%= flash_class(key) %>">
      <button class="close" data-dismiss="alert">×</button>
      <%= value %>
    </div>
  <% end %>
<% end %>
```
看到第一行`if flash.any?`如果真的有flash被創造出來才顯示

再來看到`user_facing_flashes.each do |key, value|`
user_facing_flashes是剛剛創造的helper
如果是剛剛的例子flash.to_hash會出現{"alert"=>"Alert!"}
完整的flash.to_hash.slice "alert", "notice","warning"意思是把key等於這三個的hash都抓出來
意思是如果當前flash的key不包含這三個就會回傳空的hash
回去view看`<%= value %>`就是Alert!了！

最後`<div class="alert alert-dismissable alert-<%= flash_class(key) %>">`只是管理css
flash_class利用FLASH_CLASSES.fetch key.to_sym用key抓取value回傳
例如key是alert就會回傳她的vlaue是danger
所以render出了bootstrap的 alert-danger

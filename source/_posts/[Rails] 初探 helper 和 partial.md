---
title: '[Rails] 初探 helper 和 partial'
date: 2018-04-24 07:27:00
categories:
 - [Rails]
tags:
 - Rails
 - helper
 - partial
---
這兩個都是來避免過多邏輯在view裡面把view搞亂了，或者是重複性太高違反了Don't repeat yourself（DRY）程式碼不重複的精神。

先說使用情境：

`helper`使用在較單一情況且需要與原始程式碼進行一些邏輯混合的情況，例如if else，或者只是要format一些數據但卻沒辦法一眼讀懂。

`partial`則使用在超過兩個頁面的地方，例如重複用的表單，每個頁面幾乎都要用到的header和footer等。

## Helper使用
---
例如要實作出可以由商品的圖片進入到商品的詳細內容，但如果沒有商品的圖片則是show出預設圖片，我們可以這樣一步一步來：

``` html index.html.erb
<% @products.each do |product| %>
	<% if product.image.present? %>
		<%= image_tag(product.image.medium.url, class: "thumbnail") %>
	<% else %>
		<%= image_tag("http://placehold.it/400x400&text=No Pic", class: "thumbnail") %>
	<% end %>
<% end %>
```
寫成這樣實在讓人無法一眼明瞭，開始建立helper：
``` ruby products_helper.rb
def render_product_image_link(product)
	link_to product_path(product) do
		if product.image.present?
			image_tag(product.image.medium.url)
		else
			image_tag("http://placehold.it/400x400&text=No Pic")
		end
	end
end
```
再來修改剛剛的view:
``` html index.html.erb
<% @products.each do |product| %>
    <%= render_product_image_link(product) %>
<% end %>
```
頓時覺得空氣清淨許多！

`記住helper是全域的，即使在別的頁面也可以使用！`

## Partial使用
---
例如header，不會想要在每個頁面下都先有好幾行程式碼開頭，所以先做出一個Partial：
``` html _header.html.erb
<center><h1>Header</h1></center>
```
然後放在layout的html裡面，通常是application.html.erb
``` html application.html.erb
<body>
	<%= render "header" %>
	<%= yield %>
</body>
```
這樣每個頁面上面都會顧頂有header這個東西了！

1.不只能放在layout裡面，任何一個view都可以把他render進來。
2.如果沒給他絕對路徑會自動去app/views的這個資料夾找尋。
3.建議可以做歸類像header和footer自己創一個資料夾放進去，例如commen則<%= render "common/header" %>就好。

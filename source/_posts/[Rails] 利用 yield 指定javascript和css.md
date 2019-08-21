---
title: '[Rails]利用 yield 指定javascript和css'
date: 2018-08-09 08:46:00
categories:
 - [Rails]
tags:
 - Rails
 - yield
 - JavaScript
 - CSS
---
最基本的使用就是在controller指定layout之後載入Template樣板：
```html application.html.erb
…
<body>
	<%= yield %>
</body>
…
```

```ruby controller
class EventsController < ApplicationController
	layout "special"

	def index
		…
	end
end
```

```html event/index.html.erb
<p> Hello, world <p>
```

我們可以利用content_for自訂上述東西
```html application.html.erb
…
<body>
	<%= yield :event %>
	<%= content_for :sidebar do %>
		<p> Hello, world <p>
	<% end %>
</body>
…
```
會有一樣效果，當然通常content_for會是放在別的檔案這只是個概念，他也可以拿來做tag，許多人拿他來做一隻檔案專門加強SEO
```ruby
<%= content_for :head do %>
	<%= tag(:meta, :content => @event.name, :property => "og:title") %>
	<%= tag(:meta, :content => @event.description, :property => "og:description") %>
	<%= tag(:meta, :content => "article", :property => "og:type") %>
	<%= tag(:meta, :content => @event.logo.url, :property => "og:image") %>
	<%= tag(:meta, :content => event_url(@event), :property => "og:url") %>
<% end %>
```

## 怎麼指定載入Javascript和CSS呢？

```html application.html.erb
<!DOCTYPE html>
<html lang="zh-tw">
<head>
…
<%= yield :page_specific_css %>
…
</head>
<body>
…
<%= yield %>
…
<%= yield :page_specific_javascript %>
</body>
```

然後在該view例如剛剛的index
```ruby event/index.html.erb
<p> Hello, world <p>
<% content_for :page_specific_css do %>
	<script type="text/stylesheet”>
		p {
			color: red;
		}
		<%= stylesheet_link_tag "cssfile" %>
	</script>
<% end %>
<% content_for :page_specific_javascript do %>
	<script type="text/javascript">
		console.log(“run script”)
		<%= javascript_include_tag "jsfile" %>
	</script>
<% end %>
```
這樣當進入該頁面可以直接寫入或載入外部檔案，如果該頁面的Template沒有這些也不會噴錯，就只是沒有page_specific_css和page_specific_javascript而已，如果重複宣告content_for相同的名稱他會把它合併不會覆蓋，十分方便。

值得小注意一下的地方是如果直接在application.html宣告content_for的話會先執行template裡的東西，然後一樣由上到下執行，可以多使用console.log測試一下就知道了。

參考文章
[Rails實戰聖經](https://ihower.tw/rails/actionview.html)

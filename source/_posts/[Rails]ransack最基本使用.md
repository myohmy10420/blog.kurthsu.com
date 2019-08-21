---
title: '[Rails]Ransack最基本使用'
date: 2018-04-17 05:35:00
categories:
 - [Rails, Gem]
 - [Ransack]
tags:
 - Rails
 - Gem
 - Ransack
---
[官方文件](https://github.com/activerecord-hackery/ransack)

在view我們使用search_form_for去操作
``` html search.html.erb
<%= search_form_for @q do |f| %>
    <%= f.label :name_cont %>
    <%= f.search_field :name_cont %>
    <%= f.submit %>
<% end %>
```

在controller裡
``` javascript search_controller.rb
def index
  @q = Person.ransack(params[:q])
  @people = @q.result(distinct: true)
end
```

此時如果我們在收尋欄輸入  'kurt'  並送出在rails console會發現:
`$ params[:q]` = {’name’ => 'kurt'}

`$ @q` = Ransack::Search<class: Person, base: Grouping <conditions: [Condition <attributes: ["name"], predicate: cont, values: ["kurt"]>], combinator: and\>\>

`$ @q.result.to_sql` = "SELECT "persons".* FROM "persons" WHERE ("persons"."name" LIKE '%kurt%')"

我們可以把撈出的result放進@people這個變數拿去使用了！

`注意:在f.search_field欄位給予的欄位名稱後面要加上_cont！`
例如要找name這個欄位就要輸入name_cont

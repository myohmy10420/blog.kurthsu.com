---
title: '[Rails]form_for select簡易使用'
date: 2018-05-05 10:51:00
categories:
 - [Rails]
tags:
 - Rails
 - form_for
 - select
---
先創造一個最基本的`select`：
``` html view.html.erb
<%= form_for @profile do |f| %>
  <%= f.select(:age, options_for_select(["6+", "12+", "15+", "18+"]), {}, { :class => ’style'}) %>
<% end %>
```

我們建議把`options`寫在model：
``` ruby Profile.rb
AGE_CLASS = ["6+", "12+", "15+", "18+"].freeze
```
`常數全部大寫是個好習慣`
如此簡單的字串array我們也可以改寫為：
``` ruby Profile.rb
AGE_CLASS = %w(6+ 12+ 15+ 18+).freeze
```

把options放到view上：
``` html view.html.erb
<%= form_for @profile do |f| %>
  <%= f.select(:age, options_for_select(Profile::AGE_CLASS), {}, { :class => ’style'}) %>
<% end %>
```
---

如果是想用`key, value`的話options改為：
``` ruby Profile.rb
AGE_CLASS = [["普遍級","6+"], ["保護級","12+"], ["輔導級","15+"], ["限制級","18+"]].freeze
```
---

如果想要類似placeholder的空值的話利用`include_blank`：
``` html view.html.erb
<%= form_for @profile do |f| %>
  <%= f.select(:age, options_for_select(Profile::AGE_CLASS), {:include_blank => "請選擇"}, { :class => ’style'}) %>
<% end %>
```
---

但這樣似乎會讓edit帶入不了表單之前的值，所以我們用預設值`selected`：
``` html view.html.erb
<%= form_for @profile do |f| %>
  <%= f.select(:age, options_for_select(Profile::AGE_CLASS, :selected => @profile.age), {:include_blank => "請選擇"}, { :class => ’style'}) %>
<% end %>
```
這樣如果像是新增或者是先前表單的值並不在options裡面則會跳回include_blank
---

如果想用html先驗證必填的話加入`required`：
``` html view.html.erb
<%= form_for @profile do |f| %>
  <%= f.select(:age, options_for_select(Profile::AGE_CLASS, :selected => @profile.age), {:include_blank => "請選擇"}, { :class => ’style', :required => true}) %>
<% end %>
```
---

如果想讓某個options不能選擇的話加入`disabled`
``` html view.html.erb
<%= form_for @profile do |f| %>
  <%= f.select(:age, options_for_select(Profile::AGE_CLASS, :selected => @profile.age, :disabled => "18+"), {:include_blank => "請選擇"}, { :class => ’style', :required => true}) %>
<% end %>
```
甚至可以在裡面寫ruby code，但建議邏輯判斷不要寫在view裡就是了，可以善用helper！

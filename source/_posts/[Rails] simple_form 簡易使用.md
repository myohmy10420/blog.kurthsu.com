---
title: '[Rails] simple_form 簡易使用'
date: 2018-04-15 09:04:00
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - simple form
---
[官方文件](https://github.com/plataformatec/simple_form)

## 安裝
---
``` javascript Gemfile
gem 'simple_form'
```
`$ bundle install` 安裝gem
`$ rails generate simple_form:install` 安裝simple_form
`$ rails generate simple_form:install —bootstrap` 安装 simple_form for bootstrap
如果有在使用bootstrap的話simple_form是很方便支援的

再來就可以開始使用了！

## 使用
---
### 把form_fo改為simple_form_for及可套入
``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :username %>
  <%= f.input :password %>
  <%= f.button :submit %>
<% end %>
```

### 現在可以追加幾種用法
``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :username, label: 'Your username please', error: 'Username is mandatory, please specify one' %>
  <%= f.input :password, hint: 'No special characters.' %>
  <%= f.input :email, placeholder: 'user@domain.com' %>
  <%= f.input :remember_me, inline_label: 'Yes, remember me' %>
  <%= f.button :submit %>
<% end %>
```
`label` 設定標題-default欄位名稱
`error`在model設定validates後統一出現的錯誤訊息
`hint`在input欄位下方的提示訊息
`placeholder`沒有特別的
`inline_label`給欄位為布林值用的-checkbox, radiobox

### 再來可以設定任何的css
``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :username, label_html: { class: 'my_class' } %>
  <%= f.input :password, hint: false, error_html: { id: 'password_error'} %>
  <%= f.input :password_confirmation, label: false %>
  <%= f.button :submit %>
<% end %>
```
如果有安裝了boostrap可以直接用framework的css

### 也可以使用html tag原生的東西
``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :username, input_html: { class: 'special' } %>
  <%= f.input :password, input_html: { maxlength: 20 } %>
  <%= f.input :remember_me, input_html: { value: '1' } %>
  <%= f.button :submit %>
<% end %>
```

### 想要宣染整個表單每一個欄位的設定的話在最外層加入defaults
``` html form.html.rb
<%= simple_form_for @user, defaults: { input_html: { class: 'default_class' } } do |f| %>
  <%= f.input :username, input_html: { class: 'special' } %>
  <%= f.input :password, input_html: { maxlength: 20 } %>
  <%= f.input :remember_me, input_html: { value: '1' } %>
  <%= f.button :submit %>
<% end %>
```

### 使用wrapper_html可以把一個input欄位的css模組化
``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :username, wrapper_html: { class: 'username' } %>
  <%= f.input :password, wrapper_html: { id: 'password' } %>
  <%= f.input :remember_me, wrapper_html: { class: 'options' } %>
  <%= f.button :submit %>
<% end %>
```

### 現在validates不一定要寫在model可以直接寫在html裡面
官方文件敘述
>Required fields are marked with an * prepended to their labels.

>By default all inputs are required. When the form object includes ActiveModel::Validations (which, for example, happens with Active Record models), fields are required only when there is presence validation. Otherwise, Simple Formwill mark fields as optional. For performance reasons, this detection is skipped on validations that make use of conditional options, such as :if and :unless.
>And of course, the required property of any input can be overwritten as needed

``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :name, required: false %>
  <%= f.input :username %>
  <%= f.input :password %>
  <%= f.button :submit %>
<% end %>
```

### 可以直接更改input type
``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :username %>
  <%= f.input :password %>
  <%= f.input :description, as: :text %>
  <%= f.input :accepts,     as: :radio_buttons %>
  <%= f.button :submit %>
<% end %>
```
經由checkbox轉變的`as: :radio_buttons`會自動出現yes/no的按鈕
`as: :select`可以製造出dropdown效果

官方補充敘述
>So instead of a checkbox for the accepts attribute, you'll have a pair of radio buttons with yes/no labels and a textare instead of a text field for the description. You can also render boolean attributes using as: :select to show a dropdown.

### simple_form支持所有Rails tag原生功能
``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :date_of_birth, as: :date, start_year: Date.today.year - 90,
                              end_year: Date.today.year - 12, discard_day: true,
                              order: [:month, :year] %>
  <%= f.input :accepts, as: :boolean, checked_value: true, unchecked_value: false %>
  <%= f.button :submit %>
<% end %>
```

### simple_form可以把label, hint, input_field, error and full_error等等拆開讓他們各自是一個tag
詳情請[閱讀官方文件](https://github.com/plataformatec/simple_form)！
官方敘述
>Simple Form also allows you to use label, hint, input_field, error and full_error helpers (please take a look at the rdocs for each method for more info
``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :date_of_birth, as: :date, start_year: Date.today.year - 90,
                              end_year: Date.today.year - 12, discard_day: true,
                              order: [:month, :year] %>
  <%= f.input :accepts, as: :boolean, checked_value: true, unchecked_value: false %>
  <%= f.button :submit %>
<% end %>
```

### 特別的Collections功能
可以簡易的做出一個數字範圍的select-不用輸入as: :select
``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :user %>
  <%= f.input :age, collection: 18..60 %>
  <%= f.button :submit %>
<% end %>
```

`Collections`可以為一個陣列-也可以搭配兩個參數客製化select, radiobox等等
`label_method` -顯示標題
`value_method` -實際得到的值

實際例子
``` html form.html.rb
<%= simple_form_for @user do |f| %>
  <%= f.input :gender, as: :radio_buttons, collection: [['0', 'female'], ['1', 'male']], label_method: :second, value_method: :first %>
<% end %>
```
這時候會製造出兩個radiobox，label是second每個陣列的第二個，實際的值則是first第一個！


## 實際經驗
---
狀況1.
把submit加上disable_with可以在按鈕disable的時候更改敘述文字讓使用者更好懂
``` html form.html.rb
    <div class="form-actions">
      <%= f.submit "Submit", disable_with: "Submiting...", class: "btn btn-primary"%>
    </div>
```

狀況2.
假如route是有層級的
例如:/group/1/post/1
則需要對simple_form傳入陣列且順序不能錯！
``` html form.html.rb
  <%= simple_form_for [@group,@post] do |f| %>
    <div class="form-group">
      <%= f.input :content, label: "內容" input_html: { class: "form-control"} %>
    </div>
    <div class="form-actions">
      <%= f.submit "Submit", disable_with: "Submiting...", class: "btn btn-primary"%>
    </div>
  <% end %>
```

希望能幫助到大家，有錯誤再請不吝嗇指教，當然還有更多文件請[參考官方文件](https://github.com/plataformatec/simple_form)！

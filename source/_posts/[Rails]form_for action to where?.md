---
title: '[Rails]form_for action to where?'
date: 2018-04-16 06:42:00
categories:
 - [Rails]
tags:
 - Rails
 - form_for
---
參考文章:
[Ruby on Rails Guides](http://guides.rubyonrails.org/form_helpers.html#binding-a-form-to-an-object)
[Ruby on Rails 5.2.0](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html)
[form_for but to post to a different action(stackoverflow)](https://stackoverflow.com/questions/5320414/form-for-but-to-post-to-a-different-action)

## 如何看結果
---
直接以[Ruby on Rails Guides](http://guides.rubyonrails.org/form_helpers.html#binding-a-form-to-an-object)為例子
``` html form.html.erb
<%= form_for @person, url: {action: "create"} do |person_form| %>
  <%= person_form.text_field :name %>
  <%= fields_for @person.contact_detail do |contact_detail_form| %>
    <%= contact_detail_form.text_field :phone_number %>
  <% end %>
<% end %>
```
開啟遊覽器按檢查可以直接看到form_for render出的form tag長得如何
```
<form class="new_person" id="new_person" action="/people" accept-charset="UTF-8" method="post">
  <input name="utf8" type="hidden" value="&#x2713;" />
  <input type="hidden" name="authenticity_token" value="bL13x72pldyDD8bgtkjKQakJCpd4A8JdXGbfksxBDHdf1uC0kCMqe2tvVdUYfidJt0fj3ihC4NxiVHv8GVYxJA==" />
  <input type="text" name="person[name]" id="person_name" />
  <input type="text" name="contact_detail[phone_number]" id="contact_detail_phone_number" />
</form>
```
再來我們就專注於form這個tag就好！


## 原理
---
如剛剛的例子:
1.form_for會先去查看instance是什麼
2.預設去找尋對應的model存不存在
3.如存在會去找對應名稱的controller
4.`when new action create, when edit action update`

所以此例子會有model/persons.rb和person_controller.rb，且controller應該至少如下:

``` javascript person_controller.rb
def new
    @person = Person.new
end

def create
    @person = Person.find(params[:id])
    @person = Person.new(person_params)
    @person.save
end

private

def person_params
    params.require(:person).permit(:參數)
end
```

## 自定義
---
在form_for裡面可以設定
`url`:
controller-指定controller
action-指定用controller裡的哪個method
params-傳遞什麼params

`method`:指定用什麼動作(get, post...等)

例如:
``` html form_html.erb
 <%= simple_form_for @person, url: {:controller => 'my_controller', :action => 'my_action'}, method: 'post' do |f| %>
```
更多實際例子可以參考這篇[form_for but to post to a different action(stackoverflow)](https://stackoverflow.com/questions/5320414/form-for-but-to-post-to-a-different-action)

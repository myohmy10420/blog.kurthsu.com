---
title: '[Rails]Know the gem meta_tags'
date: 2020-01-07 23:21:06
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - meta_tags
---

其實 SEO 一直都很簡單, 叫流行的 gem 大概是 [MetaTags](https://github.com/kpumuk/meta-tags) 和 [SEO Helper](https://github.com/ARCANEDEV/SEO-Helper), 這篇記錄一下怎麼使用以及原理

## 參考環境
ruby -> 2.6.3
rails -> 5.2.3

## 安裝
在 Gemfile 新增後 bundle install
再來一行指令 `$ rails generate meta_tags:install`
但我不知道為什麼我安裝失敗, 直接去複製官方網站的[meta_tags.rb](https://github.com/kpumuk/meta-tags/blob/master/lib/generators/meta_tags/templates/config/initializers/meta_tags.rb)到自己的`config/initializers/`也可以

## 基本使用
有三種使用方法
1. 直接在 view 使用

```html page.html
<head>
  <%= display_meta_tags(title: 'Member Login',
                        description: 'Member login page.',
                        keywords: 'Site, Login, Members') %>
</head>
```
好處是最簡單, 壞處當然是比較沒彈性, 流程和判斷基本上要包在 helper 裡面不然很肥一包在 html 裡面降低維護性

2. 在 controller 使用

```html page.html
<head>
  <%= display_meta_tags %>
</head>
```
```ruby page_controller.rb
def show
  set_meta_tags(title: 'Member Login',
                description: 'Member login page.',
                keywords: 'Site, Login, Members')
end
```
`注意 view 裡的 display_meta_tags 帶參數的話會覆蓋過去, 因為 view 的helper 是比 controller 的晚跑`

好處是很好處理邏輯, 即使沒有 set_meta_tags 也不會發生什麼事, 很彈性, 我比較喜歡這種方式

3. 在 model 使用

```html page.html
<head>
  <%= display_meta_tags %>
</head>
```
```ruby document.rb
class Document < ApplicationRecord
  def to_meta_tags
    {
      title: title,
      description: summary,
    }
  end
end
```
```ruby page_controller.rb
def show
  @document = Document.find(parmas[:id])
  set_meta_tags @document
end
```
好處是跟 controller 一樣, 而且把邏輯整理到 model 裡面更簡潔, 就看團隊覺得這種事在 controller or model 哪裡做比較好即可

## 運作原理
1. 最終 render meta tags 的 method 是 `view_helper.rb 的 display_meta_tags`
2. 所有的操作都是在更新 `@meta_tags 這個由 MetaTags::MetaTagsCollection 這個 class 所生成的 instance value`
3. 然後用 `MetaTags::Renderer` 去生成 html meta tags(會在 display_meta_tags 被呼叫)
4. 而更新 @meta_tags 的 method 是 `controller_helper.rb 的 set_meta_tags`

最後值得一提的是, 我們可以在 model 裡面直接更新 @meta_tags 是因為在下面這段 code 裡:
```ruby meta_tags_collection.rb
def update(object = {})
  meta_tags = object.respond_to?(:to_meta_tags) ? object.to_meta_tags : object
  @meta_tags.deep_merge! normalize_open_graph(meta_tags)
end
```
利用`respond_to?(:to_meta_tags)`這邊的判斷, 而 respond_to? 可以判斷這個 instance value 有沒有這個 method 可以呼叫
簡單來說如果傳入的 object 可以執行 object.to_meta_tags 就會回傳 true 了

---
title: '[Rails]cancan的load_and_authorize_resource'
date: 2018-08-08 04:13:00
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - cancan
---
這邊簡單記錄一下第一次碰到load_and_authorize_resource的歷程，打這篇文章之前發現已經有大神把cancan講解得非常完美了，參考[Cancan 實作角色權限設計的最佳實踐](http://blog.xdite.net/posts/2012/07/30/cancan-rule-engine-authorization-based-library-2/)

`load_and_authorize_resource`在官方有提到的是等於
```ruby
   def load_and_authorize_resource
      load_resource
      authorize_resource
    end
```
要不要拆開來用都是可以的，拆開不拆開他們都有很多不一樣的用法

## load_resource
`load_resource`會幫你把所有的methods載入instance value，而載入哪一個則預設看controller的naming，例如PostsController則會載入@post

`很智能的是它會判斷controller是什麼`
例如是new則@post = Post.new
例如是show則@post = Post.find(params[:id])

## authorize_resource
`authorize_resource`則是對resource的權限判斷
例如在Ability檔案裡有
```ruby
  can :read, Post
  can :create, Post
  can :update, Post
```
則在剛剛PostController使用這三個methods是沒問題的

這時候可能遇到需要在一個controller去判斷不同的model，那要怎麼判斷權限呢？

```ruby
lass PostController < ApplicationController
  authorize_resource :comment

  def new
    @comment = Comment.new
  end

  def show
    @comment = Comment.find(params[:id])
  end
end
```
authorize因為找不到instance value會自動產生Comment的instance value，雖然就可以在PostController判斷comment的權限，但還是寫清楚一點好

你可以把load_resource看成先製作好所有instance value
而authorize_resource則是去檢查current_user對於此instance value的權限
最直覺的當然就是
```ruby
load_resource
authorize_resource
```

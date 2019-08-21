---
title: '[Rails]利用 request direct_to 原本頁面'
date: 2018-04-15 05:49:00
categories:
 - [Rails]
tags:
 - Rails
 - request
 - direct_to
---
參考文章:
[How to redirect to previous page in Ruby On Rails?(stackoverflow)](https://stackoverflow.com/questions/2139996/how-to-redirect-to-previous-page-in-ruby-on-rails/4984221)

[Ruby on Rails 實戰聖經](https://ihower.tw/rails/actioncontroller.html)

## 前言
---
`redirect_to :back`這個簡單回上一頁的方法似乎在Rails5不能使用了！
這時候我們可以利用`request`和`session`去做到導回前一頁的功能。

這裡的狀況:
有A和B頁面都可以過去C頁面編輯一篇文章，但從A頁面編輯完後要回到A，B則回到B，也就是說要做到這樣子:

A > C > A
B > C > B

如何實作？

## 實作
---
在C的controller裡的edit和update加入兩行
``` javascript C_controller.rb
  def edit
    @article = Article(params[:id])
    session[:return_to] ||= request.referer
  end

  def update
    @article = Article.find(params[:id])
    if @article.update(post_params)
      redirect_to session[:return_to]
      session.delete(:return_to)
      flash[:notuce] = "Update Success"
    else
      redirect_to root, alert: "Something wrong..."
    end
  end

  private

  def post_params
    params.require(:article).permit(:content)
  end
```
原理是用request.referer把前一頁的網址先存在session裡面，update的時候在導回剛剛存的網址頁。

所以我在A頁面的時候對某篇文章按編輯到C頁面觸發他的edit方法，這時候把前一頁也就是A的網址存下來，編輯完送出後觸發C的update導回剛剛存的A網址頁面，同理B也是一樣，我們就可以做到:

A > C > A
B > C > B

另外剛剛的update可以簡寫如下:
``` javascript
  def update
    @article = Article.find(params[:id])
    if @article.update(post_params)
      redirect_to session.delete(:return_to), notice: "Update Success"
    else
      redirect_to root, alert: "Something wrong..."
    end
  end
```

## 補充
---
request是用戶端發送請求的東西，例如header等等，當然也有response對象，可以參考

[Ruby on Rails 指南](https://ruby-china.github.io/rails-guides/action_controller_overview.html#the-request-and-response-objects)

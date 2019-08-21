---
title: '[Rails]從零做起 simple line bot'
date: 2018-04-25 15:11:00
categories:
 - [Rails]
 - [Line]
tags:
 - Rails
 - Line
---
## 設置我們自己的第一個API
---
首先我們先創造一個專案並且利用postman做出第一個簡單的api
`$ rails new simple line bot`

到router裡面建立入口
``` ruby routes.rb
Rails.application.routes.draw do
  post "webhook", to: "welcome#webhook"
end
```

建立controller
`$ rails g controller welcome`

``` ruby welcome_controller.rb
class WelcomeController < ApplicationController
	def webhook
		head: ok 200
	end
end
```
至少要回傳一個簡單的東西這邊是 head: ok 200

再來打開server
`$ rails s`

利用postman的`post`送出一個請求到本地端剛剛的街口`localhost:3000/webhook`

這時候看剛剛terminal小黑窗會看到因為`CSRF`而出現的錯誤
Can’t verify CSRF token authenticity

這是遊覽器的一個保護機制，如果沒有這個token則有心人士可以不透過遊覽器去呼叫網站的API，例如資料填一填post到一個網址即可用別人的信用卡幫自己買東西

為了先解決這個問題先修改controller
``` ruby welcome_controller.rb
class WelcomeController < ApplicationController
	protect_from_forgery with: :null_session
	def webhook
		head: ok 200
	end
end
```
這時候postman在post到localhost:3000/webhook應該就可以得到回傳的200了！

## 部署到heroku
---
到heroku官方網站 https://dashboard.heroku.com/apps 創造一個app
跟github的指令一樣到自己的專案push上去

在本地端查看server log
`$ heroku logs -t`

然後用postman測試post到`https://{app name}.herokuapp.com/webhook`
terminal應該也可以看到回傳了ok 200

## 申請並且設定line bot
---
首先到line developers https://developers.line.me/en/ 申請一個bot channel

在Basic information找到`Channel secret`
在Messaging settings找到`Channel access token`等等要使用

然後在Messaging settings設定
Use webhooks設定為Enabled
Webhook URL Requires SSL設定網址，就是剛剛heroku的網址`https://{app name}.herokuapp.com/webhook`。

所以其實把自己的機器人加為好友後傳訊息給他會先到Line的server端，就是這裡的後台，他會post到剛剛的Webhook URL就是自己的heroku伺服器，這時候看自己的heroku logs應該也會看到訊息！

## 抓取回傳一樣對話的訊息：
---
如果我們現在發對話給我們機器人，自己的heroku logs應該會看到訊息而訊息格式如下(此為官方範例)：
```
{
	"events": [
		{
			"replyToken": "0f3779fba3b349968c5d07db31eab56f",
			"type": "message",
			"timestamp": 1462629479859,
			"source": {
				"type": "user",
				"userId": "U4af4980629…"
			},
			"message": {
				"id": "325708",
				"type": "text",
				"text": "Hello, world"
			}
		},
		{
			"replyToken": "8cf9239d56244f4197887e939187e19e",
			"type": "follow",
			"timestamp": 1462629479859,
			"source": {
				"type": "user",
				"userId": "U4af4980629..."
			}
		}
	]
}
```

我們其實就可以拿這個回傳的params做事情了！

假設我們只是要拿對話可以先print 出來在server logs上面觀察
``` ruby welcome_controller.rb
class WelcomeController < ApplicationController
	protect_from_forgery with: :null_session
	def webhook
		puts params["events"].first["message"]["text"]
		head: ok 200
	end
end
```
此時應該看得到自己傳的訊息。

## 安裝line-bot-sdk-ruby套件並使用
---
line-bot-sdk-ruby官方文件https://github.com/line/line-bot-sdk-ruby

Gemfile 安裝完後bundle install 修改controller

``` ruby welcome_controller.rb
class WelcomeController < ApplicationController
	protect_from_forgery with: :null_session
	def webhook
		# Line Bot API 物件初始化
		client = Line::Bot::Client.new { |config|
			config.channel_secret = ENV['line_channel_secret']
			config.channel_token = ENV['line_channel_token']
		}

		reply_token = params['events'][0]['replyToken']

		response_message = {
			type: "text",
			text: params["events"].first["message"]["text"]
		}

		client.reply_message(reply_token, response_message)
		head :ok
	end
end
```
`client.reply_message`會再去post到LINE server的套件function
格式就是帶兩個參數在上面都寫明了！

這部分就先commit再push到heroku上面吧！

`注意到這裡需要設定ENV環境參數`在下一步講解。

## 設定heroku環境參數
---
到heroku網帳進去到自己bot的專案裡面 > settiing > 把Config Variables打開
到LINE developers自己的channel找到`Channel secret`和`Channel access token`

新增兩個選項：
`line_channel_secret` - `Channel secret`
`line_channel_token` - `Channel access token`

這樣就大功告成摟！
